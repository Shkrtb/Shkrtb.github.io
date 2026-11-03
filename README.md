# Shkrtb.github.io

就職活動用の個人ポートフォリオサイト。
公開URL：https://shkrtb.github.io/

- Hugo（extended、v0.169.0）で生成する1ページ構成のサイト（テーマは使っていない）
- `main` ブランチに push すると、GitHub Actions が自動でビルド・公開する

## 作業内容（初回構築）

| 項目 | 内容 |
|---|---|
| 構成 | テーマを使わない自作の1ページ構成（日本語のみ） |
| 文章 | `data/` 内の YAML ファイルに分離し、テンプレートを触らずに更新できるようにした |
| 見た目 | `assets/css/style.css`。スマートフォン（幅600px以下）向けの表示と、OSのダークモードに対応 |
| 動画 | 制作物のYouTube動画を埋め込み（`youtube-nocookie.com` を使用） |
| 研究の図 | 現時点では掲載していない。追加用の枠を用意している（後述） |
| 公開 | `.github/workflows/hugo.yml`（push 時に自動でビルド・公開） |

## ファイル構成

```
hugo.toml                  サイト全体の設定（タイトル、説明文、URL）
data/
  profile.yaml             氏名・所属・研究分野・連絡先
  research.yaml            研究（テーマ、概要、取り組み、図、動画）
  publications.yaml        発表・受賞
  skills.yaml              スキル
  works.yaml               制作物
layouts/
  baseof.html              全ページ共通の枠（head、フッター）
  home.html                トップページの構成
  404.html                 存在しないURLを開いたときのページ
  _partials/youtube.html   YouTube 埋め込み部品
assets/css/style.css       デザイン
static/images/research/    研究の図・写真を置く場所
.github/workflows/hugo.yml 自動公開の設定
```

## 自分で更新する方法

### 1. 手元で表示を確認する

```powershell
cd C:\Users\krtbs\Documents\Shkrtb.github.io
hugo server
```

ブラウザで http://localhost:1313/ を開く。ファイルを保存すると表示が自動で更新される。終了は `Ctrl + C`。

### 2. 文章を変える

`data/` 内の該当ファイルを編集する。

- 発表を追加する：`data/publications.yaml` の先頭に、既存の項目と同じ形でブロックを追加する
- 論文へのリンクを付ける：`data/publications.yaml` の該当項目で `url: ""` に URL を書く（例：`url: "https://..."`）。題目がリンクになる
- 受賞を追加する：該当項目の `awards:` に `- 賞の名前` を追加する（ない場合は `awards: []`）
- スキルを追加する：`data/skills.yaml` の `items: [...]` に追記する
- 制作物を追加する：`data/works.yaml` に既存の項目と同じ形でブロックを追加する。動画がない場合は `youtube:` と `video_note:` の行を削除する

YAML は字下げ（スペース）に意味がある。タブは使わず、既存の行と字下げをそろえる。
`#` や `:` を含む文字列は `"C#"` のようにダブルクォートで囲む。

### 3. 研究の図・写真を追加する

1. 画像ファイル（JPEG または PNG。横幅1600px程度までに縮小しておくと表示が速い）を `static/images/research/` に置く
   例：`static/images/research/device.jpg`
2. `data/research.yaml` の `figures: []` を次のように書き換える

   ```yaml
   figures:
     - src: images/research/device.jpg
       alt: 試作したイヤホン型端末
       caption: 図1 試作したイヤホン型端末
   ```

   - `src`：`static/` より後ろのパス
   - `alt`：画像が表示できないときや読み上げ時に使う説明文
   - `caption`：画像の下に表示する説明（不要なら行ごと削除）
   - 複数ある場合は `- src:` のブロックを続けて書く
3. 図は研究の「主な取り組み」の下に表示される

研究の動画（YouTube）を載せる場合は、`videos: []` を次のように書き換える。
動画IDは `https://www.youtube.com/watch?v=XXXXXXXXXXX` の `v=` 以降の部分。

```yaml
videos:
  - id: XXXXXXXXXXX
    title: 動画のタイトル
```

研究の図・動画を公開する前に、研究室の内部情報や未発表の内容が含まれていないか、指導教員に確認する。

### 4. 公開する

```powershell
git add -A
git status          # 変更内容を確認
git commit -m "発表を追加"
git push
```

push すると GitHub Actions が動き、1〜2分程度で https://shkrtb.github.io/ に反映される。
進み具合は https://github.com/Shkrtb/Shkrtb.github.io/actions で確認できる。

### 5. 公開後の表示確認

1. https://github.com/Shkrtb/Shkrtb.github.io/actions を開き、最新の「Deploy Hugo site to Pages」が緑のチェックになっていることを確認する（赤い×の場合はクリックしてエラー内容を見る）
2. https://shkrtb.github.io/ を開く。古い表示のままなら `Ctrl + F5` で再読み込みする
3. スマートフォンで同じURLを開き、文字のはみ出しや横スクロールがないか確認する
   （PCの Chrome / Edge では `F12` → 端末アイコン（`Ctrl + Shift + M`）でスマートフォン幅の表示も確認できる）
4. ページ内リンク（研究・発表・受賞など）、GitHub・ソースコードのリンク、メールのリンク、動画の再生を確認する

### Hugo のバージョンを上げる場合

手元の Hugo を更新したら、`.github/workflows/hugo.yml` の `HUGO_VERSION` も同じ値に変更する。
