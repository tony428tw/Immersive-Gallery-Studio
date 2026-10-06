# ROADMAP.md

# Immersive-Gallery-Studio Roadmap

Legend:

- `[ ]` not started
- `[-]` in progress
- `[x]` completed only after acceptance criteria and quality gates pass

## Current Status

- [ ] Context Audit
- [ ] Phase 0 — Foundation
- [ ] Phase 1 — Desktop Editor
- [ ] Phase 2 — Artwork & Exhibition Tools
- [ ] Phase 3 — Gallery Experience
- [ ] Phase 4 — WebXR Viewer
- [ ] Phase 5 — XR Editor
- [ ] Phase 6 — Cloud Save & Publishing
- [ ] Phase 7 — Performance & Quest Optimization
- [ ] Phase 8 — Hand Tracking
- [ ] Phase 9 — Mixed Reality
- [ ] Phase 10A — AI Curator
- [ ] Phase 10B — Realtime Collaboration
- [ ] Phase 10C — Spatial Analytics
- [ ] Final System Validation

---

## Context Audit

Goal:

- Codex reads repository documentation.
- scans repository state.
- reports available / missing context.
- makes no file changes unless explicitly asked.

Output:

```text
# Immersive-Gallery-Studio Context Audit

## ChatGPT Context
## Repository Status
## Existing Files
## Existing Architecture
## Project Understanding
## Core Architecture Rules Understood
## Missing Context
## Recommended Documentation
## AGENTS.md Compliance
## Ready for Phase 0
```

---

## Phase 0 — Foundation

Goal:

Create stable logical scene architecture.

Implement:

- React / TypeScript / Vite project foundation if needed
- Three.js / R3F
- Zustand
- Zod
- Vitest
- GalleryScene schema
- SceneStore
- SelectionStore
- EditorStore
- HistoryStore
- Command / CommandManager
- basic commands
- SceneRenderer
- Floor
- Wall
- Artwork placeholder
- Light
- serialize / deserialize
- basic demo scene
- OrbitControls

Acceptance:

- Zod validates Scene JSON.
- Store renders scene.
- serialize → deserialize preserves transforms.
- MoveObjectCommand supports execute/undo/redo.
- Renderer can recreate visuals entirely from store data.

Do not implement Phase 1 UI.

---

## Phase 1 — Desktop Editor

Goal:

Usable desktop editing shell.

Implement:

- toolbar
- viewport
- selection
- TransformControls
- Property Inspector
- Scene Tree
- grid
- snapping
- keyboard shortcuts
- IndexedDB
- autosave
- restore

Acceptance:

- select/move/rotate/scale
- Inspector edits use Commands
- undo/redo
- hide/show
- lock
- duplicate/delete
- refresh restores scene

---

## Phase 2 — Artwork & Exhibition Tools

Goal:

Turn the editor into a real exhibition curation tool.

Implement:

- image upload
- physical width/height
- aspect ratio lock
- artwork metadata
- wall snap
- frame system
- measurements
- basic alignment/distribution
- GLB/GLTF
- Asset Library

Acceptance example:

1200×800 image + width 1.2 m → height 0.8 m.

Artwork must snap correctly to non-axis-aligned walls using wall normal math.

---

## Phase 3 — Gallery Experience

Goal:

Public online 3D gallery.

Implement:

- public gallery route
- 3D walkthrough
- WASD
- first-person navigation
- artwork hover / info
- Presentation Mode
- Info Hotspot
- Navigation Hotspot
- guided tour foundation
- loading/error UI

Acceptance:

Public viewer can open and navigate exhibition without Editor UI.

---

## Phase 4 — WebXR Viewer

Goal:

View same gallery in Meta Quest 3.

Implement:

- WebXR capability service
- xrStore
- immersive-vr
- XR adapters
- controllers
- ray selection
- input map
- teleport
- snap turn
- world-space artwork info

Acceptance on Quest 3:

- HTTPS opens
- Enter VR appears when supported
- same scene appears
- controller ray works
- teleport works
- snap turn works
- artwork info opens
- exit VR returns safely

---

## Phase 5 — XR Editor

Goal:

Edit exhibition directly in Quest 3.

Implement:

- Explore/Edit modes
- XR selection
- grab preview
- commit MoveObjectCommand on release
- wall snap reuse
- XR rotation
- uniform scale
- radial menu
- undo/redo
- locked-object feedback

Critical Acceptance:

1. Edit artwork in Quest.
2. Save/commit same Scene.
3. Reopen on Desktop.
4. Exact persistent transform matches.

This is the MVP milestone.

---

## Phase 6 — Cloud Save & Publishing

Goal:

Real project management and public publishing.

Implement:

- backend repository interfaces
- Supabase integration
- auth
- gallery records
- asset storage
- Draft / Published
- versions
- permissions
- public URLs
- QR

Acceptance:

Owner publishes; anonymous viewer can view but not edit; Quest opens same published gallery.

---

## Phase 7 — Performance & Quest Optimization

Goal:

Scale to larger exhibitions.

Implement:

- DesktopHigh / MobileMedium / QuestPerformance
- Scene Profiler
- texture variants
- GLB analysis
- warnings
- LOD
- room/zone streaming
- lazy loading

Acceptance:

Remote rooms do not preload all full-resolution assets on Quest.

---

## Phase 8 — Hand Tracking

Goal:

Controller-free basic interaction.

Implement:

- XRHandInput
- hand ray
- point
- pinch
- grab
- world-space UI support
- experimental two-hand uniform scale

Acceptance:

Without controllers, user can select artwork, open info, enter edit mode, move artwork, operate essential UI.

---

## Phase 9 — Mixed Reality

Goal:

Place exhibition artwork on real surfaces.

Implement:

- VR/MR mode
- runtime MR capability checks
- passthrough
- plane detection if supported
- real-wall selection
- true-scale placement
- anchor support if available
- session-only fallback if persistence unavailable

Acceptance:

Artwork physical dimensions match real-world scale.

---

## Phase 10A — AI Curator

Goal:

AI-assisted curation while preserving Undo.

Implement:

- typed proposal schema
- room/wall/artwork context
- placement proposal
- preview
- accept/reject/regenerate
- CompositeCommand

Acceptance:

Accepted AI layout can be undone as one logical action.

---

## Phase 10B — Realtime Collaboration

Goal:

Multi-user editing.

Implement:

- CollaborationAdapter
- presence
- selected object
- room
- ray/cursor
- object locks
- lock timeout
- operation/patch synchronization
- conflict rules

Acceptance:

No full-scene resend for each transform movement.

---

## Phase 10C — Spatial Analytics

Goal:

Use platform for exhibition / spatial behavior analysis.

Implement:

- anonymized sessions
- sampled position/orientation
- route
- dwell time
- viewing events
- viewing distance
- room use
- artwork exposure
- wall utilization
- 2D heatmap
- dashboard

Artwork-view rule must consider:

- distance
- view direction
- visibility
- duration

Acceptance:

Analytics runs without writing to database every render frame.

---

## Final System Validation

Full workflow:

```text
Create Exhibition
↓
Create Room
↓
Upload Artwork
↓
Wall Snap
↓
Measure
↓
Lighting
↓
Save
↓
Publish
↓
Desktop Gallery
↓
Quest VR
↓
XR Edit
↓
Desktop Sync
↓
Hand Tracking
↓
MR
↓
AI Layout
↓
Collaboration
↓
Analytics
```

Quality gates:

```bash
npm run typecheck
npm run lint
npm run test
npm run build
```
