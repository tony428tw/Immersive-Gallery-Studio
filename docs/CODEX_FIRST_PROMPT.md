# Codex First Prompt — Context Audit

你現在要接手專案：

`Immersive-Gallery-Studio`

## 目前不要寫程式

第一個任務只有「讀取專案上下文 + Repository 稽核」。

請完整讀取：

1. `AGENTS.md`
2. `README.md`
3. `docs/CHAT_HANDOFF.md`
4. `docs/IMMERSIVE_GALLERY_SPEC.md`
5. `docs/ARCHITECTURE.md`
6. `docs/ROADMAP.md`

然後掃描目前 Repository。

請不要：

- npm install
- 修改檔案
- 建立元件
- 重構
- 開始 Phase 0
- 提前實作後續 Phase

請確認你理解以下不可破壞原則：

```text
Data ≠ Renderer ≠ UI ≠ Interaction
```

以及：

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

並確認：

- Scene Data 是 Single Source of Truth。
- Desktop / Gallery / Quest 3 / XR 共用同一 Scene Data。
- 所有永久編輯必須經過 Command。
- Desktop 與 XR 共用 CommandManager / History。
- Three.js Mesh/Object3D 不是永久資料來源。
- 世界單位使用 meter。
- WebXR 採 runtime capability detection。
- 不得提前執行下一個 Phase。
- Phase 完成前必須通過 typecheck / lint / test / build。

完成後請只回報：

```text
# Immersive-Gallery-Studio Context Audit

## 1. Repository Status
- Is repository empty?
- Existing framework / package.json?
- Existing src?
- Existing docs?
- Existing AGENTS.md?
- Existing Git history if visible?

## 2. Documents Read
列出實際讀取的文件。

## 3. Existing Architecture
說明目前程式架構；若無則明確寫尚未建立。

## 4. Project Understanding
用你的話摘要產品與技術目標。

## 5. Core Architecture Rules Understood
逐項確認。

## 6. Risks / Conflicts
指出目前 Repository 與規格是否衝突。

## 7. Missing Context
若無，寫 None。不要猜測不存在的資訊。

## 8. Ready for Phase 0
YES / NO

若 NO，說明阻塞原因。
```

完成 Context Audit 後停止，等待我明確下達 Phase 0 指令。
