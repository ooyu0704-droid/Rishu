# Rishu プロジェクトルール

> このファイルは Claude Code が参照するルール集です。200行以内を維持してください。

---

## ファイル操作の原則

<file-management>
- フォルダ内のファイルを勝手に削除しない
- 既存ファイルを上書きしない
- 更新・修正が必要な場合は、別ファイルとして保存する
  - ファイル名末尾に更新日を yyyymmdd 形式で付与する
  - 例：`rishu_home_v4_20260407.html`、`DESIGN_20260407.md`
- 新しいHTMLバリアントは `rishu_home_v{N}.html` 形式で連番管理する
</file-management>

---

## デザインシステム

<design-system>
### カラーパレット（必ず遵守）
| トークン | 値 | 用途 |
|---|---|---|
| Cyan（Primary Accent） | `#00C9FF` | ボタン・強調・アクティブ状態 |
| Gold（Secondary Accent） | `#FFD700` | バッジ・シンクロ率・ハイライト |
| Background | `#0A0A0F` | ページ背景 |
| Surface | `#12121A` | カード・モーダル背景 |
| Text Primary | `#FFFFFF` | 見出し |
| Text Secondary | `#A0A0B0` | 本文・補足テキスト |

### レイアウト原則
- **モバイルファースト**：基準幅 390px（iPhone 15 Pro相当）
- ダークモード固定（ライトモード対応は Phase 2 以降）
- アイコンは Material Design Icons を使用

### Stitch 生成時の必須指定
プロンプトには必ず以下のブロックを含める：
```
**DESIGN SYSTEM (REQUIRED):**
- Platform: Mobile, Mobile-first, 390px
- Theme: Dark, anime/gaming aesthetic, vibrant on dark
- Background: Near Black (#0A0A0F)
- Surface: Dark Gray (#12121A) for cards
- Primary Accent: Electric Cyan (#00C9FF) for CTAs
- Secondary Accent: Gold (#FFD700) for sync badges
- Text: White (#FFFFFF) headings, Muted (#A0A0B0) body
- Icons: Material Design Icons
- Roundness: Rounded (12px cards, 8px buttons)
```
</design-system>

---

## コーディング規約

<coding>
### HTML / Tailwind CSS v4
- Tailwind CSS v4 の CDN 版を使用（`@tailwindcss/browser`）
- カスタムカラーは Tailwind の `@theme` ブロックで定義し、ハードコードしない
- クラス名はモバイルファーストで記述（`md:` `lg:` でデスクトップ拡張）

### i18n（国際化）対応
- UIテキストはハードコーディングしない方針（将来の多言語対応を見据える）
- コメントで翻訳キーを明示する：`<!-- i18n: sync.rate.label -->`
</coding>

---

## Git ・ブランチ管理

<git>
### マルチWorktreeの運用ルール
このリポジトリは `git worktree` によるパラレルブランチ構成を採用しています。
Worktreeはメインリポジトリの**兄弟ディレクトリ**として配置します。

```
Workspace/
  Rishu/           ← main（メインリポジトリ）
  gifted-pascal/   ← claude/gifted-pascal
  jolly-perlman/   ← claude/jolly-perlman
  jolly-rubin/     ← claude/jolly-rubin
```

| Worktree | ブランチ名 | パス | 用途 |
|---|---|---|---|
| Rishu | `main` | `Workspace/Rishu/` | 本番・リリース |
| jolly-perlman | `claude/jolly-perlman` | `Workspace/jolly-perlman/` | 作業ブランチ1 |
| gifted-pascal | `claude/gifted-pascal` | `Workspace/gifted-pascal/` | 作業ブランチ2 |
| jolly-rubin | `claude/jolly-rubin` | `Workspace/jolly-rubin/` | 作業ブランチ3 |

### コミットルール
- 1コミットの変更は **120行以内** を目安にする
- コミットメッセージは日本語 OK、プレフィックスを使う：
  - `feat:` 新機能、`design:` UI変更、`fix:` バグ修正、`docs:` ドキュメント
- **main への直接プッシュは禁止**。必ず PR を経由する
</git>

---

## Claude Code の使い方

<claude-code>
### プランモード（必須場面）
以下のタスクは必ず **Shift+Tab でプランモードを起動** してから実行する：
- 新しい画面の Stitch 生成
- HTML の大規模変更（50行以上）
- デザインシステムの変更（カラー・コンポーネント追加）
- 複数ファイルにまたがる変更

### スキルの活用
`~/.claude/skills/` に以下のスキルが利用可能：

| スキル | コマンド | 用途 |
|---|---|---|
| stitch-design | `/stitch-design` | 画面の新規生成・編集（推奨エントリーポイント） |
| enhance-prompt | `/enhance-prompt` | Stitch プロンプトの品質向上 |
| design-md | `/design-md` | 既存デザインから DESIGN.md を生成 |

### モデルの使い分け
- **Opus**：設計・計画タスク（プランモード時）
- **Sonnet**：HTML実装・Tailwindスタイリング・Stitch編集

### コンテキスト管理
- Stitch生成後は `/compact` でコンテキストを圧縮する
- 別画面・別タスクへ切り替えるときは `/clear` を実行する
- コンテキスト使用率が **50%を超えたら `/compact` を検討**する

### Figma MCP の活用
- デザイン参照：`get_design_context` でフレーム・コンポーネントの構造を取得
- コード生成：FigmaフレームIDを指定してHTML/Tailwind CSS変換
- アセット取得：`get_screenshot` で視覚確認、SVGエクスポートに対応
- Stitch連携：Figmaコンポーネントを参照しながら `/stitch-design` プロンプトを生成

### デバッグ
- 動作確認は **Playwright MCP** でスクリーンショットを取得する（390px幅）
- コンソールエラーは `mcp__playwright__browser_console_messages` で確認する
</claude-code>

---

## プロジェクト固有の注意事項

<project-notes>
### MVP フォーカス（コミケPOC段階）
現在は実装しない：ライトモード対応、デスクトップ専用レイアウト、i18nの実装

### シンクロ機能のデザイン優先度
「シンクロ率」と「共通作品ハイライト」は **Gold (#FFD700) で最強調**。
視覚的なインパクトがコアバリューに直結するため、デザイン品質を妥協しない。

### Stitch プロジェクトとの関係
- 生成デザインは `.stitch/designs/` に保存（ファイル操作ルール遵守）
- DESIGN.md は `.stitch/DESIGN.md` に配置し、全画面の一貫性を維持する
</project-notes>
