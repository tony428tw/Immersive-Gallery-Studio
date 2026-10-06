# CHAT_HANDOFF.md

## Purpose

This file transfers the planning context previously developed in ChatGPT into the `Immersive-Gallery-Studio` repository so Codex does not need access to the original conversation.

## Project Name

`Immersive-Gallery-Studio`

The name is fixed and should be used consistently in:

- Repository naming
- README
- documentation
- application branding
- Codex prompts

## Product Definition

Immersive-Gallery-Studio is not merely a 3D viewer.

It is a:

> Web-based XR Exhibition Studio

The target workflow is:

```text
Create Exhibition
↓
Create / Select Gallery Space
↓
Upload Artwork
↓
Place Artwork
↓
Wall Snap / Measure / Align
↓
Lighting
↓
Save
↓
3D Preview
↓
Quest 3 WebXR Preview
↓
Edit in VR
↓
Return to Desktop
↓
Publish
↓
Share by URL / QR
```

## Supported Experiences

### Desktop Editor

Desktop browser used for detailed curation and editing.

Core capabilities:

- Scene editor
- Property Inspector
- Scene Tree
- Asset Library
- Transform controls
- Measurement
- Wall snap
- Lighting
- Save / Undo / Redo

### Online Gallery

Public browser experience.

Core capabilities:

- 3D walkthrough
- artwork information
- presentation mode
- hotspots
- guided tour
- WebXR entry

### Meta Quest 3

Quest Browser accesses the same published gallery.

Required:

- WebXR immersive VR
- controller ray
- teleport
- snap turn
- artwork information
- XR edit mode
- select / grab / move / rotate / scale
- shared Scene Data with Desktop

### Hand Tracking

After controller interaction is stable:

- hand ray
- point
- pinch
- grab
- basic world-space UI
- optional two-hand scaling

### Mixed Reality

Later phase:

- passthrough
- runtime capability detection
- plane detection when available
- artwork placement on real walls
- true physical scale
- anchors when supported
- safe fallback when persistence is unavailable

## Critical Product Difference

The key differentiator is not simply:

> "Quest can view the gallery."

The real target is:

```text
Desktop can create the exhibition
+
Web can view the exhibition
+
Quest can immersive-walk the exhibition
+
Quest can directly edit the exhibition
+
Desktop and Quest use the same Scene Data
```

## Data Architecture Decision

A logical Scene Document is the source of truth.

Do not use Three.js Object3D as permanent data.

Expected model:

```text
Scene Document
├─ environment
├─ rooms
├─ objects
├─ artworks
├─ lights
├─ hotspots
├─ navigation
├─ tour
└─ xr
```

Internal 3D world unit:

`meter`

UI may convert display to:

- mm
- cm
- m

## Interaction Architecture

Mouse, touch, controllers, and hands should converge into one logical interaction layer.

Conceptual input adapters:

```text
InteractionManager
├─ DesktopInput
├─ TouchInput
├─ XRControllerInput
└─ XRHandInput
```

Shared event concept:

```ts
interface InteractionEvent {
  source: "mouse" | "touch" | "controller" | "hand";
  type: "hover" | "select" | "grab" | "release" | "activate";
  targetId?: string;
}
```

## Command Architecture

All persistent editing operations must be undoable.

Examples:

- AddObjectCommand
- RemoveObjectCommand
- MoveObjectCommand
- RotateObjectCommand
- ScaleObjectCommand
- ChangeArtworkCommand
- ChangeMaterialCommand
- DuplicateObjectCommand
- CompositeCommand

Desktop and XR share the same history.

## Artwork Requirements

Initial artwork formats:

- JPG
- JPEG
- PNG
- WebP
- GLB
- GLTF

Later:

- video
- audio
- 360 panorama
- PDF
- interactive web content

For flat artwork:

- preserve aspect ratio
- allow actual physical width input
- calculate physical height
- use meter internally
- support frames
- support wall snapping
- support physical measurement

## Wall Snap

Wall snapping must use geometry math rather than hard-coded cardinal directions.

Expected basis:

- wall normal
- vector math
- quaternion
- configurable small surface offset to avoid z-fighting

The same wall-snap engine must serve Desktop and XR.

## Quest Interaction Direction

VR has two modes:

### Explore Mode

- navigate
- inspect
- view artwork information

### Edit Mode

- select
- grab
- move
- rotate
- scale
- duplicate
- delete
- lock
- undo
- redo

Initial locomotion:

- Teleport
- Snap Turn

Smooth movement can be added later.

## Performance Direction

Quest 3 is the principal XR performance target.

Future performance profiles:

- DesktopHigh
- MobileMedium
- QuestPerformance

Optimization areas:

- texture variants
- draw calls
- triangle count
- shadows
- light count
- model analysis
- LOD
- lazy loading
- room/zone streaming

## Publishing Direction

Later cloud architecture should separate:

- Draft
- Published

Publishing creates version snapshots.

Public users read Published scene data.

Potential backend:

- Supabase Auth
- PostgreSQL
- Storage
- Realtime

Keep repository abstractions between UI and backend provider.

## AI Direction

AI Curator must never directly mutate SceneStore.

Correct workflow:

```text
AI
↓
Proposal
↓
Preview
↓
User Accept
↓
Commands / CompositeCommand
↓
SceneStore
```

This makes AI actions undoable.

## Collaboration Direction

Future multiplayer editing should synchronize operations/patches, not resend an entire `.gallery.json` for every small movement.

Potential tools:

- Yjs
- WebSocket
- Supabase Realtime

Object-level temporary locks should prevent simultaneous conflicting transforms.

## Spatial Analytics Direction

Future analytics module should be separated from renderer logic.

Potential metrics:

- visitor position
- route
- route length
- dwell time
- room dwell time
- artwork viewing time
- artwork viewing distance
- viewing direction
- artwork visibility
- exposure count
- wall utilization
- heatmap

Viewing should not be inferred from distance alone; direction, visibility, and duration should also be considered.

## Model / Codex Usage Strategy

The project is being developed under a ChatGPT Plus plan.

Recommended usage concept:

- strongest reasoning model: architecture audits, WebXR/Quest/MR, difficult state synchronization
- Sol-class model: main implementation
- faster model: repetitive, low-risk edits, tests, documentation and cleanup

Do not waste high-cost reasoning on routine formatting and minor UI changes.

## Immediate Next Action

Before Phase 0:

1. Codex reads all repository docs.
2. Codex performs Context Audit.
3. Codex does not modify files during Context Audit.
4. After the audit, the user explicitly authorizes Phase 0.
