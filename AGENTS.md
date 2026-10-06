# AGENTS.md

## Project

Project name: `Immersive-Gallery-Studio`

This repository implements a Web-based XR Exhibition Studio for desktop browsers, online galleries, Meta Quest 3 WebXR, XR editing, MR, AI-assisted curation, collaboration, and spatial analytics.

## Mandatory Reading

Before modifying code, read:

1. `README.md`
2. `docs/CHAT_HANDOFF.md`
3. `docs/IMMERSIVE_GALLERY_SPEC.md`
4. `docs/ARCHITECTURE.md`
5. `docs/ROADMAP.md`

When these documents conflict, preserve the architectural principles in this file first, then report the conflict before making large structural changes.

## Non-negotiable Architecture Rules

### 1. Single Source of Truth

Scene data is the source of truth.

Three.js / React Three Fiber objects are render representations only.

Never use a Mesh, Group, Object3D, Camera, Controller, or XR object as the authoritative persistent application state.

### 2. Strict Separation

Maintain:

```text
Data ≠ Renderer ≠ UI ≠ Interaction
```

Expected flow:

```text
User Input
↓
Interaction Manager
↓
Command
↓
Scene Store
↓
Scene Document
↓
Renderer
↓
Three.js / WebXR
```

### 3. Desktop and XR Share the Same Scene

Desktop Editor, Online Gallery, Quest 3 VR, Hand Tracking, and future MR must use the same logical Scene Document.

Do not create a separate "VR scene" or duplicate scene model.

### 4. All Persistent Editing Uses Commands

Persistent edit actions must be represented by Commands.

Examples:

- AddObjectCommand
- RemoveObjectCommand
- MoveObjectCommand
- RotateObjectCommand
- ScaleObjectCommand
- ChangeArtworkCommand
- ChangeMaterialCommand
- CompositeCommand

Commands must support reversible history.

Do not let UI or XR input directly commit permanent Mesh transforms.

### 5. Preview vs Commit

Interactive dragging, gizmos, controller grab, and hand manipulation may use transient preview transforms.

At interaction end:

```text
Preview
↓
Command
↓
SceneStore
↓
Renderer
```

Do not create one History entry per render frame.

### 6. Shared History

Desktop and XR use the same CommandManager / HistoryStore.

Do not create separate XR history.

### 7. World Units

All persistent 3D coordinates and dimensions use meters.

UI may display mm / cm / m.

### 8. XR Feature Detection

Use runtime capability detection.

Do not rely on user-agent strings as the main WebXR capability mechanism.

Quest-specific behavior must live behind adapters instead of scattered `if (isQuest)` checks.

### 9. Phase Discipline

Only implement the currently requested Phase.

Do not silently add later-phase functionality.

Minor interfaces/placeholders that preserve forward compatibility are allowed, but they must not become hidden partial implementations.

### 10. Build Discipline

Before declaring a Phase complete, run:

```bash
npm run typecheck
npm run lint
npm run test
npm run build
```

Fix failures first.

### 11. Code Quality

- TypeScript strict mode.
- Avoid `any`.
- Prefer small modules over giant files.
- Do not place the application in one `App.tsx`.
- Do not create one giant `Scene.tsx`.
- Scene schemas use TypeScript + Zod.
- Public architecture changes must be documented.

### 12. No Architecture Workarounds

If a feature conflicts with the architecture:

1. identify the root cause,
2. explain the conflict,
3. preserve Scene Data / Command / Store / Renderer boundaries,
4. then implement the smallest durable solution.

Do not use shortcuts that break later Desktop/XR synchronization.

## Expected Source Layout

```text
src/
├─ app/
├─ editor/
│  ├─ toolbar/
│  ├─ inspector/
│  ├─ asset-library/
│  ├─ scene-tree/
│  └─ viewport/
├─ gallery/
├─ engine/
│  ├─ renderer/
│  ├─ objects/
│  ├─ materials/
│  ├─ lighting/
│  ├─ navigation/
│  └─ interaction/
├─ scene/
│  ├─ schema/
│  ├─ commands/
│  ├─ migration/
│  └─ serialization/
├─ xr/
│  ├─ core/
│  ├─ controllers/
│  ├─ hands/
│  ├─ locomotion/
│  ├─ ui/
│  ├─ editing/
│  └─ adapters/
├─ analytics/
├─ stores/
├─ assets/
├─ services/
├─ repositories/
├─ hooks/
├─ types/
└─ utils/
```

This may evolve, but major deviations must be justified in `docs/ARCHITECTURE.md`.

## Phase Completion Report

After each Phase, report:

```text
## Phase X Status

Completed:
-

New Files:
-

Modified Files:
-

Architecture Changes:
-

Tests:
-

Build:
-

Known Issues:
-

Technical Debt:
-

Recommended Next Step:
-
```
