# Immersive-Gallery-Studio

Immersive-Gallery-Studio 是一套以 Web 為核心的沉浸式虛擬展間、線上藝廊與 WebXR 策展編輯平台。

## 核心目標

同一套 Scene Data 支援：

- Desktop 3D Editor
- Online Gallery
- Meta Quest 3 WebXR
- VR 內直接編輯展覽
- Hand Tracking
- Mixed Reality
- Cloud Publishing
- AI Curator
- Realtime Collaboration
- Spatial Analytics

## 核心技術

- React
- TypeScript
- Vite
- Three.js
- React Three Fiber
- @react-three/drei
- @react-three/xr
- Zustand
- Zod
- IndexedDB / Dexie

後續階段：

- Supabase
- PostgreSQL
- Storage / Auth / Realtime
- Yjs

## 核心架構原則

```text
Data ≠ Renderer ≠ UI ≠ Interaction
```

資料流：

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

SceneStore / Scene Document 為唯一真實資料來源。

Desktop、Gallery、Quest 3 與未來 MR 不得建立彼此獨立的 Scene Data。

## 先讀文件

Codex 每次開始工作前，請依序讀取：

1. `AGENTS.md`
2. `README.md`
3. `docs/CHAT_HANDOFF.md`
4. `docs/IMMERSIVE_GALLERY_SPEC.md`
5. `docs/ARCHITECTURE.md`
6. `docs/ROADMAP.md`

## 開發階段

- Phase 0 — Foundation
- Phase 1 — Desktop Editor
- Phase 2 — Artwork & Exhibition Tools
- Phase 3 — Gallery Experience
- Phase 4 — WebXR Viewer
- Phase 5 — XR Editor
- Phase 6 — Cloud Save & Publishing
- Phase 7 — Performance & Quest Optimization
- Phase 8 — Hand Tracking
- Phase 9 — Mixed Reality
- Phase 10A — AI Curator
- Phase 10B — Realtime Collaboration
- Phase 10C — Spatial Analytics

目前正式開發前，先執行 Context Audit，再開始 Phase 0。

## 每個 Phase 完成條件

必須通過：

```bash
npm run typecheck
npm run lint
npm run test
npm run build
```

若任一失敗，不得宣告 Phase 完成。
