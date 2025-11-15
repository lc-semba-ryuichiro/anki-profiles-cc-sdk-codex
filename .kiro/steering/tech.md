# Technology Stack

## Architecture
Vite 5 をベースにしたシングルページ React アプリ。CSR を基本としつつ、暗記フローやカード生成などドメインロジックはクライアント側 Zustand ストアへ集約する。SDK 化を想定し、UI 層とデータモデル層は疎結合に保つ。

## Core Technologies
- **Language**: TypeScript (strict モード、ES2023 target)
- **Framework**: React 19 + React StrictMode
- **Runtime/Build**: Node.js 20 系での pnpm 10.21、Vite 5 bundler
- **Styling**: Tailwind CSS + PostCSS（グローバル CSS は `src/index.css` で初期化）
- **State**: Zustand（将来的な複数ストアでの暗記進捗共有を想定）

## Key Libraries
- `@vitejs/plugin-react` + `babel-plugin-react-compiler` による最新 React 最適化
- `zustand`：カード状態やレビューキュー管理
- Storybook + Chromatic：UI カタログとビジュアルリグレッション
- Vitest + Testing Library + MSW：ユニット・DOM・API モックテスト
- Textlint / Remark：日本語ドキュメント品質検証
- Lefthook + Commitlint：コミット規約と自動フック

## Development Standards

### Type Safety
`tsconfig.json` で `strict`, `noUncheckedIndexedAccess`, `exactOptionalPropertyTypes` などを有効化。`allowJs: false` で JS 流入を防ぎ、`useUnknownInCatchVariables` でエラーハンドリングを明示する。

### Code Quality
ESLint（functional, security, sonarjs, strict-dependencies などプラグイン多数）と Prettier（sort-imports, tailwindcss プラグイン）を併用。Stylelint や Secretlint, Dependency Cruiser も導入済みで、コード／スタイル／構成ファイルを一貫して自動チェックする。

### Testing
Vitest を標準テストランナーとし、Testing Library DOM/React でレンダリング検証、MSW で API レイヤーをスタブ。Stryker によるミューテーションテストで暗記アルゴリズムの堅牢性を担保する想定。Storybook + Chromatic は視覚回帰とアクセシビリティ (addon-a11y) の検証に利用する。

## Development Environment

### Required Tools
- Node.js 20 以上
- pnpm 10.21 系（`packageManager` フィールドに準拠）
- Vite, Vitest, Storybook CLI（pnpm 経由で取得）

### Common Commands
```bash
pnpm dev      # 開発サーバー (Vite)
pnpm build    # tsc -b + vite build による本番ビルド
pnpm lint     # ESLint で全体を検証
```
（テスト・Storybook 用スクリプトは `package.json` に追加予定。現状は `pnpm vitest` / `pnpm storybook dev` を直接呼び出す。）

## Key Technical Decisions
- pnpm catalog を活用し依存ライブラリのバージョン統一と更新管理を容易化
- React Compiler プラグインを初期段階から有効化し、再レンダリングの自動最適化を前提に設計
- Zustand で状態を集約し、将来的な SDK 抽出時にストアをそのままエクスポート可能にする
- Lefthook で commit/push 時に ESLint/Textlint/Secretlint などを自動実行し、CI 前に品質を担保

---
_Generated: 2025-11-15T14:57:15Z_
