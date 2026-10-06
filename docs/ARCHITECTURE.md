# ARCHITECTURE.md

# Immersive-Gallery-Studio Architecture

## 1. Architectural Goal

The system must remain maintainable as it grows from a desktop editor into WebXR, MR, AI, collaboration, and analytics.

The most important design decision is:

```text
Data ≠ Renderer ≠ UI ≠ Interaction
```

## 2. Canonical Data Flow

```text
User Input
↓
Interaction Adapter
↓
Interaction Manager
↓
Command / CommandManager
↓
SceneStore
↓
Scene Document
↓
Renderer
↓
Three.js / R3F / WebXR
```

Renderer observes state; it does not own persistent state.

## 3. Scene Document

The Scene Document must be:

- serializable
- versioned
- validated
- independent of Three.js runtime objects
- portable across Desktop / Gallery / XR / future MR

Preferred persistent format:

`.gallery.json`

## 4. Store Responsibilities

Suggested stores:

### sceneStore

Owns current GalleryScene.

### selectionStore

Owns selected logical object ID(s).

### editorStore

Owns editor mode, active tool, grid/snap preferences.

### historyStore

Owns or exposes command history state.

### assetStore

Later: asset metadata, loading state, local/cloud references.

### xrStore

Later: XR support, session state, locomotion preferences, input mode.

### uiStore

Only UI state that is not persistent scene content.

Avoid one giant global store.

## 5. Commands

Commands represent persistent edits.

### Correct transform interaction

```text
Pointer/controller drag starts
↓
temporary preview transform
↓
drag ends
↓
create one Move/Rotate/Scale Command
↓
execute / commit
↓
SceneStore updates
↓
Renderer reflects canonical state
```

### Incorrect

```text
Every frame
↓
write permanent Mesh transform
↓
push history entry
```

## 6. Renderer

Renderer maps logical scene data to visual components.

Conceptually:

```text
SceneRenderer
├─ WallRenderer
├─ FloorRenderer
├─ ArtworkRenderer
├─ ModelRenderer
└─ LightRenderer
```

The exact component names may differ.

Visual components may hold ephemeral rendering refs, but persistent business state stays outside them.

## 7. Serialization

Required direction:

```text
GalleryScene
↓
validate
↓
serialize
↓
.gallery.json
```

and:

```text
.gallery.json
↓
parse
↓
Zod validate
↓
migrate if needed
↓
GalleryScene
↓
SceneStore
↓
Renderer
```

## 8. Migration

Scene has a version field from Phase 0.

Migration implementation can remain minimal initially, but the folder / interface should permit:

```text
v1 → v2 → v3
```

without breaking old projects.

## 9. Interaction

Unify all input paths.

```text
DesktopInput
TouchInput
XRControllerInput
XRHandInput
        ↓
InteractionManager
        ↓
logical actions
```

Logical scene behavior must not need to know whether the source was a mouse or Quest controller unless device-specific UX truly requires it.

## 10. XR

WebXR is not a second application.

```text
Same SceneStore
Same Scene Document
Same Artwork logic
Same Commands
Different camera/input/presentation layer
```

### Adapters

Quest-specific behavior should be hidden behind an adapter where practical.

Possible:

```text
StandardWebXRAdapter
QuestAdapter
```

Do not spread user-agent checks through scene components.

## 11. Wall Snap

Implement once in a reusable domain/service module.

Consumers:

- Desktop drag
- Inspector-assisted placement
- XR controller grab
- future hand grab
- AI Curator placement proposals

The algorithm should work from wall geometry / surface normal rather than fixed world-angle assumptions.

## 12. Assets

Logical Scene Data should reference assets, not embed huge binaries.

Early local mode can use browser object URLs / IndexedDB references carefully.

Cloud phase introduces Storage references through repositories.

## 13. Gallery vs Editor

The public Gallery should reuse Scene Renderer but should not need Editor UI.

Avoid importing the whole editor bundle merely to render a published gallery where possible.

## 14. Backend Boundary

UI and scene logic should call abstractions such as repositories/services.

Example:

```text
Editor
↓
GalleryRepository
↓
SupabaseGalleryRepository
↓
Supabase
```

This keeps provider-specific concerns replaceable.

## 15. Analytics Boundary

Analytics observes interactions / positions through defined hooks/services.

Do not insert database writes directly inside Three.js render loops.

Use sampling, buffering, and batched upload later.

## 16. AI Boundary

AI returns typed proposals.

The application converts accepted proposals into Commands.

AI never receives direct SceneStore mutation privileges.

## 17. Collaboration Boundary

Collaboration synchronizes logical operations / patches.

Transform previews can be throttled for presence, while final committed operations must preserve consistency.

## 18. Testing Priorities

High-value tests:

- schema validation
- serialization round-trip
- command execute/undo/redo
- wall-snap math
- transform commit rules
- scene migration
- published/draft boundaries later
- XR editing persistence later

## 19. Quality Gates

Every Phase:

```bash
npm run typecheck
npm run lint
npm run test
npm run build
```

Do not update ROADMAP phase status to complete unless all required acceptance criteria pass.
