# Project Structure

## Organization Philosophy
React + Vite のシンプルな構成から出発し、暗記体験に紐づく単位（プロフィール収集、カード生成、レビュー）ごとに feature-first でディレクトリを増やす。エントリーポイントやテーマなど横断的なレイヤーは `src/` 直下にまとめ、Zustand ストアやユーティリティは domain-agnostic なモジュールとして再利用する。

## Directory Patterns

### Application Source
**Location**: `/src/`
**Purpose**: エントリーポイント (`main.tsx`)、ルートコンポーネント (`App.tsx`)、グローバルスタイル (`index.css`) を配置。各 feature は `src/features/<domain>/` として追加し、UI／hooks／stores をその配下にまとめる。
**Example**:
```ts
// src/main.tsx
createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

### Assets & Public
**Location**: `/src/assets/`, `/public/`
**Purpose**: ビルドにバンドルする SVG や画像は `src/assets/` からインポート。CDN 配信したい静的ファイルや manifest は `public/` に置き、Vite がルートへコピーする。
**Example**: `import reactLogo from './assets/react.svg'`

### Styling Tokens
**Location**: `/src/index.css`, `/src/App.css`
**Purpose**: グローバル CSS でテーマ/タイポグラフィ/リセットを定義し、Tailwind 導入後も design tokens をここに集約。コンポーネント固有のスタイルは CSS modules もしくは Tailwind utility で管理。

### Documentation & Knowledge
**Location**: `/docs/`, `.kiro/specs/`
**Purpose**: Markdown ベースのアーキテクチャノートや仕様書を `/docs/` 配下に蓄積。アクティブな仕様は `.kiro/specs/<feature>/` に置き、開発の判断材料として参照する。

## Naming Conventions
- **Files**: React コンポーネントは `PascalCase.tsx`、ロジック/フック/ストアは `camelCase.ts`
- **Components**: default export のみ、機能名 + `View`/`Section` など文脈が分かる語尾
- **Hooks/Stores**: `use` で始める（例: `useProfileCardsStore`）

## Import Organization
```ts
import { useState } from 'react'
import reactLogo from './assets/react.svg'

// feature 内では階層相対パスを使用し、深さが増える場合は index barrel を作る
import { ProfileCard } from '../components/ProfileCard'
```
**Path Aliases**:
- 現状なし。将来的に `@/` → `src/` を導入する場合は `tsconfig.json` と `vite.config.ts` の双方で設定する。

## Code Organization Principles
- エントリーポイント (`main.tsx`) では UI 構成のみを行い、状態初期化や依存注入は feature 層に委譲
- Zustand ストアは `src/features/<domain>/stores/` に配置し、副作用は subscribe middleware か React Query などに任せて分離
- 路線追加時でも `App.tsx` はコンポーネント構成・ルーティングのみに集中させ、ビジネスロジックを直接書かない
- Docs/Specs/Steering を更新してから実装に入ることで、暗記フローと SDK 化における整合性を保つ

---
_Generated: 2025-11-15T14:57:52Z_
