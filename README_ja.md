# PARA Knowledge Base for Obsidian

> **LLM as Knowledge Compiler**
> Karpathy の llm-wiki パターンを、PARA ベースの Obsidian vault 向けに再構成した、ナレッジベース用テンプレート兼スキル集です。

---

## 概要

このリポジトリは、Obsidian vault を検索対象のファイル置き場ではなく、LLM が継続的に整理・更新できる知識ベースとして運用するためのひな型です。

単にノートを大量に置いておくだけでは、エージェントは毎回 vault 全体を読み直し、構造を再発見し、同じ内容を何度も要約し直すことになります。
このプロジェクトでは、その問題を避けるために以下を揃えています。

- PARA 向けのナレッジベース運用ルール
- `AGENTS.md` による vault スキーマ定義
- `_index.md` と `0. Common/index.md` による階層インデックス
- `kb-*` スキルによる取り込み・検索・監査・再索引
- Obsidian CLI / Markdown / Bases / Canvas まわりの補助スキル

このリポジトリは「プラグイン配布物」というより、Obsidian vault の初期化と運用を支えるスキャフォールドです。

---

## 背景

発想の元になっているのは Karpathy の llm-wiki です。
ポイントは、LLM を検索エンジンとして毎回生データに当てるのではなく、一度コンパイルした知識を継続的に保守する存在として使うことにあります。

Obsidian の個人用 vault は、巨大な文書コーパスよりも少しずつ育つ知識ベースに近いため、この方式と相性が良いです。

---

## なぜ必要か

PARA である程度整理された vault があっても、エージェントは会話のたびに次のようなコストを払います。

**知識ベースなし:**
```text
ユーザー: 「分散システムについて何を知っている？」
エージェント: 関連しそうな 30 個以上のノートを読む → 要約する → セッション終了で文脈は消える
次の週にまた質問される
エージェント: 同じ 30 個以上のノートをまた読む
```

**このプロジェクトを使う場合:**
```text
/kb-ingest
  → 新規ノートを分類・移動・リンク・索引化

/kb-query "distributed systems"
  → index を読んで対象ノートだけに絞る
  → 少ない読み込みで回答する
```

### 既存の vault に対して追加されるもの

| すでにあるもの | このプロジェクトが加えるもの |
|---|---|
| PARA ベースのフォルダ整理 | カテゴリごとの `_index.md` と `0. Common/index.md` |
| 断片的なタグ | タグ規約の検出と一貫性維持 |
| 手動の wikilink | 取り込み時の自動 wikilink 生成 |
| バラバラな運用ルール | `AGENTS.md` に集約された vault スキーマ |
| Inbox に溜まる未整理ノート | `/kb-ingest` による分類・移動・メタデータ付与 |
| 個別操作のノウハウ | Obsidian CLI / Markdown / Bases / Canvas 系スキル |

### トークン効率の改善例

| 操作 | ナレッジベースなし | このプロジェクトあり |
|---|---|---|
| 「今進めているプロジェクトは？」 | Projects 配下を広く読み直す | `0. Common/index.md` を起点に絞り込む |
| 「トピック X に関する知識を集めて」 | vault 全体を grep 的に探索 | index とタグ、backlink を併用して対象を限定 |
| 新しい文書の反映 | 手動で移動・タグ付け・リンク・索引更新 | `/kb-ingest` で一連の処理をまとめて実行 |
| 定期点検 | 人手で確認 | `/kb-lint` で孤立文書やリンク切れを検査 |

重要なのは、index が安く読めて、次にどこを見るべきかを明確にできることです。

---

## コンセプト

### 3 層の考え方

```text
Inbox/                          ← 生の入力
0. Common/ + 1-4 PARA folders   ← 整理・索引化された知識
AGENTS.md                       ← vault の構造と運用ルール
```

- **Inbox/** は取り込み前の着地地点です。
- **0. Common/** と **PARA ディレクトリ** は、整理済みの知識ベース本体です。
- **AGENTS.md** は、エージェントにとっての schema です。どのようなフォルダ構成か、どのタグを使うか、どう索引を保守するかを定義します。

### 3 つの基本操作

| 操作 | 役割 |
|---|---|
| **Ingest** | 新規ノートを分類し、適切な場所に統合する |
| **Query** | コンパイル済みの知識を読んで質問に答える |
| **Lint** | 構造の崩れや保守漏れを検出する |

### RAG との違い

| RAG | Knowledge Base |
|---|---|
| 問い合わせごとに断片を再取得する | 知識を一度まとめ、以後は増分更新する |
| 取得精度に品質が左右されやすい | 取り込み時に構造化されるため一貫性が出やすい |
| 要約がセッションに残りにくい | 要約とリンクが vault に残る |
| 巨大コーパス向き | 個人の知識管理や継続運用向き |

---

## 含まれる主要スキル

### `/kb-init`
Knowledge Base 層の初期化を行います。

- カテゴリごとの `_index.md` を作成
- `0. Common/index.md` を作成
- `0. Common/log.md` を作成
- `AGENTS.md` を既存ファイルに追記、またはテンプレートから作成
- Obsidian CLI の有無を確認

### `/kb-ingest`
Inbox や指定パスの文書を取り込みます。

- PARA 分類
- 移動
- frontmatter 付与
- wikilink 生成
- 関連文書の更新
- index / log 更新

### `/kb-query`
Knowledge Base を前提に検索・要約・比較を行います。

- index 起点の探索
- タグ横断検索
- backlink 探索
- 必要に応じた複数文書の統合

### `/kb-lint`
Knowledge Base の健全性を点検します。

- index drift
- 孤立文書
- タグ不整合
- broken wikilink
- frontmatter 不備
- stale content

### `/kb-index`
index を更新します。

- 差分ベースの smart update
- `--full` による全面再構築
- カテゴリ単位の更新
- dry-run による確認

---

## 同梱されている補助スキル

このリポジトリには、Knowledge Base 専用スキルだけでなく、Obsidian 作業を補助する一般スキルも含まれています。

- `obsidian-cli`
- `obsidian-markdown`
- `obsidian-bases`
- `json-canvas`
- `defuddle`
- `write-a-skill`

そのため、Knowledge Base の運用と、日常的な Obsidian 編集・検索・構造化を、同じ作業基盤の中で扱えます。

---

## リポジトリ構成

| パス | 役割 |
|---|---|
| `.claude/skills/` | Knowledge Base および Obsidian 関連スキル |
| `Templates/_index.md.template` | カテゴリ index のテンプレート |
| `Templates/AGENTS.md.template` | vault ルール用テンプレート |
| `.obsidian/` | Obsidian vault 設定 |
| `Welcome.md` | 初期ノート |

---

## セットアップ

### 使い方

このリポジトリは次のどちらかの使い方を想定しています。

1. **新しい Obsidian vault のひな型として使う**
2. **既存の Obsidian vault に必要ファイルを取り込む**

最低限、vault ルートに `.obsidian/` があり、エージェントがこのディレクトリを作業対象として開ける状態にしてください。

### PARA 構成について

このプロジェクトは、次のような PARA 構成を前提に動きます。

```text
Vault/
├── 0. Common/
├── 1. Projects/
├── 2. Areas/
├── 3. Resources/
├── 4. Archive/
└── Inbox/
```

`kb-init` は不足している構成を検出し、必要に応じて初期化を支援できます。

### 初期化後のフォルダ構成例

`kb-init` の実行後、vault は概ね次のような構成で運用されます。

```text
Vault/
├── .obsidian/
├── AGENTS.md
├── Inbox/
│   ├── clipped-article.md
│   └── meeting-note.md
├── 0. Common/
│   ├── index.md
│   ├── log.md
│   ├── daily/
│   └── weekly/
├── 1. Projects/
│   ├── _index.md
│   ├── Project Alpha/
│   │   ├── overview.md
│   │   ├── tasks.md
│   │   └── meeting-2026-05-04.md
│   └── Project Beta/
│       └── overview.md
├── 2. Areas/
│   ├── _index.md
│   ├── Paper/
│   │   ├── survey-llm.md
│   │   └── rl-note.md
│   └── Career/
│       └── resume-notes.md
├── 3. Resources/
│   ├── _index.md
│   ├── Infra/
│   │   └── kubernetes-cheatsheet.md
│   └── MLOps/
│       └── feature-store.md
└── 4. Archive/
  ├── _index.md
  └── Project Alpha/
    └── retrospective.md
```

主なポイントは次のとおりです。

- ルートには vault ルールを定義する `AGENTS.md` が置かれます。
- `0. Common/` には全体 index の `index.md` と操作ログの `log.md` が作られます。
- `1. Projects/` から `4. Archive/` には、それぞれカテゴリ単位の `_index.md` が作られます。
- `Inbox/` は未整理ノートの投入先で、`/kb-ingest` により各カテゴリへ振り分けられます。

---

## クイックスタート

1. Obsidian vault をこのリポジトリ構成で用意する
2. エージェントの作業ディレクトリとして vault ルートを開く
3. `/kb-init` を実行する
4. `Inbox/` に新規ノートを入れる
5. `/kb-ingest` を実行する
6. `/kb-query "What do I know about X?"` で問い合わせる
7. `/kb-lint` を定期実行して保守状態を確認する
8. 大きく整理し直した後は `/kb-index` を実行する

---

## Obsidian CLI 連携

Obsidian CLI が利用できる場合、検索・backlink・タグ操作がより正確になります。
CLI は必須ではありませんが、利用できると運用がかなり安定します。

### できること

| CLI コマンド | 主な用途 |
|---|---|
| `obsidian search query="term"` | 全文検索 |
| `obsidian backlinks file="Note"` | backlink 探索 |
| `obsidian tags sort=count counts` | タグ規約の把握 |
| `obsidian property:set ...` | frontmatter 更新 |
| `obsidian read ...` | ノート内容の取得 |

### CLI がない場合

CLI がなくても、スキルはファイルベースの探索にフォールバックして動作します。
その場合でも利用は可能ですが、検索や backlink 解決の精度と効率はやや下がります。

---

## 要件

- Obsidian vault
- このリポジトリのスキル群を利用できるエージェント実行環境
- 推奨: PARA 方式のフォルダ構成
- 任意: Obsidian 1.12+ と Obsidian CLI

---

## どんな人に向いているか

- Obsidian にノートは溜まっているが、AI に毎回全探索させたくない
- PARA で整理していて、index とルールを明示的に持ちたい
- Inbox からの取り込みを一貫した流れにしたい
- 単発検索ではなく、蓄積される知識ベースとして運用したい

---

## 補足

このプロジェクトの中心は「検索を速くすること」ではなく、LLM が継続的に保守できる知識構造を vault 内に持つことです。
そのため、単なる全文検索よりも、index・wikilink・frontmatter・運用ルールの整備を重視しています。