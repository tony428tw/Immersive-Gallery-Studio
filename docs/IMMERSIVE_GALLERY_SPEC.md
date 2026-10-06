# IMMERSIVE_GALLERY_SPEC.md

# Immersive-Gallery-Studio
## Product & Technical Specification

## 1. Product Vision

Build a browser-first immersive exhibition platform supporting desktop curation, public online galleries, Meta Quest 3 WebXR viewing and editing, with future MR, AI curation, collaboration, and spatial analytics.

The platform should behave as one system across devices rather than separate Desktop and VR applications.

## 2. Core Product Modes

### Editor Mode

Used by curator / designer.

Capabilities eventually include:

- scene creation
- room / wall / floor editing
- asset upload
- artwork placement
- physical dimensions
- wall snap
- alignment
- measurement
- lighting
- scene hierarchy
- undo / redo
- local and cloud save
- publishing

### Gallery Mode

Public viewing.

Capabilities:

- exhibition introduction
- artwork information
- 3D walkthrough
- presentation mode
- hotspot
- guided tour
- XR entry

### XR Mode

Meta Quest 3 / compatible WebXR.

Capabilities:

- immersive-vr
- controller support
- teleport
- snap turn
- artwork interaction
- XR edit mode
- shared scene state with Desktop

### Future MR Mode

- immersive AR / passthrough where supported
- plane-based artwork placement
- anchors where supported
- true physical scale
- graceful fallback

## 3. Technology Direction

Preferred:

- React
- TypeScript
- Vite
- Three.js
- React Three Fiber
- @react-three/drei
- @react-three/xr
- Zustand
- Zod
- Vitest
- IndexedDB / Dexie

Later:

- Supabase
- PostgreSQL
- Storage
- Authentication
- Realtime
- Yjs

## 4. Scene Model

Scene data must be serializable and independent of Three.js runtime objects.

Conceptual schema:

```ts
interface TransformData {
  position: [number, number, number];
  rotation: [number, number, number];
  scale: [number, number, number];
}

interface SceneObjectData {
  id: string;
  name: string;
  type:
    | "wall"
    | "floor"
    | "ceiling"
    | "artwork"
    | "model"
    | "light"
    | "furniture"
    | "group";
  parentId?: string;
  transform: TransformData;
  visible: boolean;
  locked: boolean;
}

interface GalleryScene {
  version: string;
  id: string;
  title: string;
  units: "meter";
  environment: unknown;
  rooms: unknown[];
  objects: SceneObjectData[];
  artworks: unknown[];
  lights: unknown[];
  hotspots: unknown[];
  navigation: unknown;
  tour?: unknown;
  xr?: unknown;
}
```

Exact Phase 0 implementation should refine unknowns into typed Zod-backed structures without overdesigning later phases.

## 5. Artwork Model

Conceptual artwork:

```ts
interface ArtworkData {
  id: string;

  type:
    | "image"
    | "video"
    | "audio"
    | "model"
    | "panorama"
    | "text";

  metadata: {
    title: string;
    artist?: string;
    year?: string;
    medium?: string;
    description?: string;
  };

  asset: {
    src: string;
    thumbnail?: string;
  };

  display: {
    width: number;
    height: number;
    frame?: string;
  };

  transform: TransformData;

  interaction: {
    showInfo: boolean;
    audioGuide?: boolean;
  };
}
```

Metadata and transform responsibilities should remain logically separable.

## 6. Units

Persistent world unit:

`meter`

Examples:

- 120 cm artwork width → 1.2 m internally
- 150 cm center height → 1.5 m internally

## 7. Editor Layout

Target desktop layout:

```text
┌───────────────────────────────────────┐
│                Toolbar                │
├───────┬─────────────────────┬─────────┤
│ Asset │                     │Property │
│       │      Viewport       │Inspector│
│       │                     │         │
├───────┴─────────────────────┴─────────┤
│ Scene / Layers / Status               │
└───────────────────────────────────────┘
```

## 8. Desktop Editor Tools

Planned:

- Select
- Move
- Rotate
- Scale
- Undo
- Redo
- Grid
- Snap
- Measure
- Preview
- XR Preview
- Save
- Publish

## 9. Architecture Objects

Initial:

- Wall
- Floor
- Ceiling

Later:

- Door
- Opening
- Column
- Partition
- Platform
- Stair

## 10. Artwork System

Initial:

- JPG
- JPEG
- PNG
- WebP
- GLB
- GLTF

Requirements:

- preserve image aspect ratio
- accept physical artwork width
- derive height automatically
- optional aspect-ratio lock
- metadata
- frames
- wall snap
- alignment
- measurement

## 11. Wall Snap

When an artwork approaches a wall:

1. detect candidate wall,
2. determine wall surface normal,
3. calculate orientation,
4. keep artwork vertical,
5. align to wall,
6. apply tiny configurable offset,
7. preserve valid physical positioning.

Do not implement only 0/90/180/270-degree wall cases.

## 12. Frame System

Planned presets:

- None
- Black
- White
- Wood
- Metal

Properties:

- frame width
- frame depth

Artwork + frame should behave as one logical artwork object.

## 13. Measurement

Planned:

- artwork width
- artwork height
- artwork center height
- artwork bottom height
- artwork spacing
- wall length
- generic distance tool later

## 14. Scene Tree

Planned actions:

- Select
- Rename
- Hide
- Show
- Lock
- Duplicate
- Delete
- Group later

## 15. Commands

Required pattern:

```ts
interface Command {
  id: string;
  name: string;
  execute(): void;
  undo(): void;
  redo(): void;
}
```

Implementation may refine signatures for store independence / dependency injection, but reversible behavior is mandatory.

History target:

at least 100 operations.

## 16. Local Persistence

Early phases:

- IndexedDB
- optional Dexie wrapper
- autosave with debounce
- restore last scene

## 17. Public Gallery Routes

Target conceptual routes:

```text
/edit/:galleryId
/gallery/:galleryId
/xr/:galleryId
```

Do not require editor UI for public Gallery rendering.

## 18. Desktop Navigation

Target:

- WASD
- mouse look
- first person
- orbit preview
- configurable eye/camera height

## 19. WebXR

Use runtime capability detection.

Do not rely on user-agent detection as primary logic.

Requirements:

- immersive-vr session
- controller ray
- teleport
- snap turn
- world-space artwork info
- graceful non-XR fallback

## 20. XR Adapter

Conceptual abstraction:

```ts
interface XRPlatformAdapter {
  initialize(): Promise<void>;
  supportsHandTracking(): boolean;
  supportsPassthrough(): boolean;
  dispose(): void;
}
```

Possible implementations:

- StandardWebXRAdapter
- QuestAdapter

Exact design may change if library APIs suggest a better typed adapter interface.

## 21. Quest Input

Centralize bindings in XRInputMap.

Initial intent:

- Trigger → select / activate
- Grip → grab
- thumbstick → locomotion / turn
- A → confirm
- B → cancel/back

Avoid scattering button assumptions across components.

## 22. XR Modes

### Explore

- navigation
- view
- info

### Edit

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

## 23. XR Editing Rule

Controller or hand transform is a transient preview while manipulation is active.

On release:

create one logical Command and commit to SceneStore.

## 24. World-space XR UI

Preferred patterns:

- floating info panels
- radial menu
- wrist menu later

Do not copy complex desktop DOM UI directly into immersive VR.

## 25. Hand Tracking

After controller editing is stable:

- hand ray
- point
- pinch
- grab
- basic UI
- two-hand uniform scale later

Hand events must feed the shared Interaction system.

## 26. Performance

Quest 3 is principal XR target.

Future profiles:

- DesktopHigh
- MobileMedium
- QuestPerformance

Track:

- triangles
- draw calls
- textures
- lights
- visible objects
- file/asset size
- estimated memory only when clearly labeled

## 27. Asset Optimization

Images should eventually produce:

- original
- large
- medium
- thumbnail

GLB analysis:

- triangles
- materials
- texture count
- file size
- bounding box

## 28. LOD / Streaming

Future:

- LOD0
- LOD1
- LOD2

Scene zones / rooms:

- Loaded
- Preload
- Unloaded

## 29. Backend

Use repository abstractions.

Potential interfaces:

- GalleryRepository
- AssetRepository
- UserRepository
- VersionRepository

Do not tightly bind UI to Supabase implementation details.

## 30. Publishing

Separate:

- Draft
- Published

Publishing creates immutable or restorable version snapshots.

## 31. Permissions

Initial roles:

- Owner
- Editor
- Viewer/Public

## 32. MR

Later only.

Runtime-detect:

- passthrough/immersive AR
- plane detection
- anchors

Provide clear fallback where APIs are unavailable.

## 33. AI Curator

AI produces proposals, not direct mutations.

Desired flow:

```text
AI Context
↓
Proposal
↓
Preview
↓
Accept
↓
CompositeCommand
↓
SceneStore
```

Possible context:

- wall geometry
- room geometry
- artwork dimensions
- metadata
- dominant colors
- themes
- existing placement
- walkable zones

## 34. Collaboration

Future:

- presence
- current room
- selection
- ray/cursor
- object-level lock
- operation / patch synchronization

Avoid full-scene resend for every transform update.

## 35. Spatial Analytics

Future:

- anonymized sessions
- route
- dwell time
- viewing events
- distance
- orientation
- visibility
- room use
- wall utilization
- heatmaps

Do not write analytics to database every render frame.

## 36. MVP Definition

MVP is Phase 0–5:

```text
Scene Engine
+
Desktop Editor
+
Artwork / Wall Snap
+
Public 3D Gallery
+
Quest 3 WebXR
+
Quest XR Editing
```

The defining acceptance test:

> Change artwork placement in Quest XR, then reopen the same scene in Desktop Editor and see the exact same persistent placement.
