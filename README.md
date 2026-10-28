# ka_metro

Beamer テーマ [metropolis](https://github.com/matze/mtheme) をベースに、日本語スライド向けに見た目と機能を調整したテンプレートです。

- バージョン: **v1.2**
- 構成ファイル
  - `ka_metro.sty` … テーマ本体（metropolis の読み込みと各種カスタマイズ）
  - `putimg.sty` … 画像を絶対位置に配置する `\putimage` コマンド（`ka_metro.sty` から自動で読み込まれる）

---

## 目次

1. [動作環境](#動作環境)
2. [基本的な使い方](#基本的な使い方)
3. [metropolis からの変更点](#metropolis-からの変更点)
4. [追加機能](#追加機能)
5. [カスタマイズ](#カスタマイズ)
6. [読み込まれるパッケージ](#読み込まれるパッケージ)
7. [注意点・既知の問題](#注意点既知の問題)
8. [更新履歴](#更新履歴)

---

## 動作環境

| 項目 | 要件 |
| --- | --- |
| エンジン | **LuaLaTeX**（`luatexja-fontspec` と `unicode-math` を使用するため。pLaTeX / pdfLaTeX では動作しません） |
| テーマ | `beamertheme-metropolis` |
| フォント | **Koruri**（`Koruri-Regular`, `Koruri-Bold`）がシステムまたは TeX から参照できること |
| 数式フォント | `LatinModernMath-Regular`（TeX Live に同梱） |
| その他 | `appendixnumberbeamer`, `textpos`, `here` など（[読み込まれるパッケージ](#読み込まれるパッケージ)参照） |

## 基本的な使い方

`ka_metro.sty` と `putimg.sty` を TeX から見える場所（例: `texmf-dist/tex/latex/beamer_mybeamer/`、または原稿と同じディレクトリ）に置き、`\usetheme` ではなく **`\usepackage`** で読み込みます。

```latex
\documentclass[aspectratio=169]{beamer}

% （任意）テーマカラーを変える場合は ka_metro より前に定義する
% \definecolor{mainframe}{RGB}{0,90,160}

\usepackage{ka_metro}

\title{タイトル}
\subtitle{サブタイトル}
\author{著者名}
\institute{所属}
\date{\today}

\begin{document}

\maketitle

\section{はじめに}

\begin{frame}{フレームタイトル}
  \begin{itemize}
    \item 項目 1
    \item 項目 2
  \end{itemize}
  \putnote{※ 補足は小さい文字で}
\end{frame}

\appendix
\begin{frame}{付録}
  付録のページは総ページ数に含まれません。
\end{frame}

\end{document}
```

コンパイル:

```sh
lualatex main.tex
```

> 総ページ数（`\inserttotalframenumber`）を正しく表示するため、2 回コンパイルしてください（`latexmk -lualatex` 推奨）。

---

## metropolis からの変更点

### テーマオプション

`ka_metro.sty` 内で metropolis を次のオプションで読み込んでいます。

| オプション | 設定値 | 意味 |
| --- | --- | --- |
| `block` | `fill` | ブロックに背景色をつける（metropolis の既定は `transparent`） |
| `progressbar` | `foot` | スライド下端にプログレスバーを表示 |
| `numbering` | `fraction` → 最終的に `none` | 右下のページ番号は消し、代わりに**フレームタイトル右側**に表示（後述） |

さらに `\usefonttheme{professionalfonts}` を指定し、数式フォントは `unicode-math` に任せています。

### フォント

- 和文・欧文ともに **Koruri**（Regular / Bold）に統一（`\setmainfont`, `\setsansfont`, `\setmainjfont`, `\setsansjfont`）。
- 数式は `unicode-math` + **Latin Modern Math**。`\setoperatorfont\symup` により `\sin` などの演算子は立体。

### 配色

| 要素 | metropolis | ka_metro |
| --- | --- | --- |
| フレームタイトル背景 | `mDarkTeal` | `mainframe`（既定: RGB(0,255,0)） |
| 本文背景 | `black!2` | `mainframe!5`（テーマカラーをごく薄く敷く） |
| プログレスバー | `mLightBrown` / 灰 | 前景 `mainframe`、背景 `mainframe!50!black!30` |
| ページ番号（タイトルバー内） | ― | `framenumcolor`（既定: 白） |
| 強調色（`\alert` の文字色） | `mLightBrown` | `AlertColor`（既定: `#EB811B` = metropolis と同じ橙） |
| alerted ブロックのタイトル | ― | 文字 `AlertColor`、背景 `AlertColor!30` |
| alerted ブロックの本文 | ― | 背景 `AlertColor!20` |
| example ブロックのタイトル | ― | 背景 `green!20!white` |
| example ブロックの本文 | ― | 背景 `green!10!white` |

テーマカラーの変更方法は[カスタマイズ](#カスタマイズ)を参照してください。

### フレームタイトル

- タイトルバーの上下余白（`\metropolis@frametitle@padding`）を **2.2ex → 1.2ex** に縮小し、本文領域を広げています。
- フォントは `\large` の**太字**。
- **ページ番号を「現在/総数」の形でタイトルバー右端に表示**します（右下のページ番号は非表示）。

### タイトルページ

metropolis のタイトルページを次のように組み直しています。

- 上から「タイトル画像（`\titlegraphic`）→ タイトル・サブタイトル → 著者 → 日付 → 所属」の順に縦方向へ均等配置。
- metropolis のタイトル下の区切り線（title separator）は**表示しない**。
- フォントサイズ: タイトル `\Large`、著者・日付 `\large`、所属 `\normalsize`。

### セクションページ

- セクション名を `\huge` で大きく表示（metropolis は `\Large`）。
- 中央寄せの 22em 幅ではなく、**本文幅いっぱい・左寄せ**のレイアウト。
- セクション名の直下にプログレスバー、その下にサブセクション名（あれば）を表示。

### ブロック

- 角丸ブロック（`\setbeamertemplate{blocks}[rounded][shadow=false]`）、影なし。
- ブロックタイトルを `\large` に拡大（metropolis は `\normalsize`）。

### 箇条書き

- 項目記号を**丸**（`[circle]`）に変更（metropolis は `\textbullet`）。
- インデントを詰める: 第 1 階層 `1.2em`、第 2 階層 `1em`。

### 余白・その他

| 項目 | 設定 |
| --- | --- |
| 左右マージン | `1em`（`text margin left/right`） |
| キャプション | `\small`、ラベル部分は太字、図のラベルは「**Fig.**」 |
| キャプション上下の空き | `5pt plus 3pt minus 2pt` |
| 参考文献 | アイコンではなく番号などのラベルを表示（`bibliography item` を `[text]`）、各項目は `\scriptsize` |
| standout フレーム | `\Large` の太字 |
| タイトル区切り線の太さ | `2pt` |
| フッターのプログレスバーの太さ | `3pt` |

---

## 追加機能

### `\emph` の再定義

`\emph{...}` は斜体ではなく **太字**（`\textbf`）になります。和文で斜体が効かない問題を避けるためです。

### `\putnote{テキスト}`

段落を改めて、`\scriptsize` の小さな文字で補足・出典などを書くためのコマンドです。

```latex
本文の内容
\putnote{出典: ○○ (2025)}
```

### `roundbox` 環境

タイトルなしで、幅と背景色を自由に指定できる角丸の箱です。

```latex
\begin{roundbox}[背景色]{幅}
  内容
\end{roundbox}
```

| 引数 | 既定値 | 説明 |
| --- | --- | --- |
| `[背景色]` | `yellow!50` | xcolor の色指定 |
| `{幅}` | （必須） | 例: `0.8\textwidth` |

文字色は黒、内側余白は `1em` です。

```latex
\begin{roundbox}[mainframe!20]{0.9\textwidth}
  ここが要点です。
\end{roundbox}
```

### `\putimage`（`putimg.sty`, v1.1 で追加）

スライドの**左上を原点**に、位置と大きさをスライドサイズに対する**割合**で指定して画像を貼ります。`textpos`（`overlay, absolute`）による絶対配置なので、本文のレイアウトには影響しません。

```latex
\putimage[オプション]{x}{y}{画像ファイル名}
```

| 引数 | 説明 |
| --- | --- |
| `x`, `y` | 画像の**左上**の位置。スライド幅・高さに対する割合（0〜1） |
| `画像ファイル名` | `\includegraphics` に渡すファイル名 |

オプション（いずれか 1 つ。省略時は原寸）:

| オプション | 説明 |
| --- | --- |
| `width=0.3` | 幅をスライド幅（`\paperwidth`）に対する割合で指定 |
| `height=0.4` | 高さをスライド高さ（`\paperheight`）に対する割合で指定 |
| `right=0.8` | 画像右端の x 座標（割合）を指定。幅は `right − x` になる |
| `scale=0.5` | 原寸に対する倍率 |

例:

```latex
\begin{frame}{結果}
  左側に説明文を書く
  \putimage[width=0.4]{0.55}{0.2}{fig/result.png}  % 右半分に配置
  \putimage[right=0.95]{0.6}{0.65}{fig/zoom.png}    % x=0.6〜0.95 の幅で配置
\end{frame}
```

### 付録ページを総ページ数から除外

`appendixnumberbeamer` を読み込んでいるため、`\appendix` 以降のフレームは総ページ数に数えられません。

---

## カスタマイズ

### テーマカラーを変える

色は `\providecolor` で定義しているので、**`\usepackage{ka_metro}` より前に** `\definecolor` しておけばそちらが優先されます。

```latex
\definecolor{mainframe}{RGB}{0,90,160}       % タイトルバー・プログレスバー・本文背景の基調色
\definecolor{framenumcolor}{RGB}{255,255,255}  % タイトルバー右端のページ番号の色
\definecolor{AlertColor}{HTML}{EB811B}         % \alert の文字色と alertblock の配色
\usepackage{ka_metro}
```

### その他の調整

読み込み後に通常の beamer / metropolis のコマンドで上書きできます。

```latex
\usepackage{ka_metro}
\setbeamerfont{frametitle}{size=\Large}
\setbeamercolor{block body}{bg=mainframe!10}
\metroset{sectionpage=none}
```

---

## 読み込まれるパッケージ

`ka_metro.sty` が自動で読み込むもの:

| パッケージ | 用途 |
| --- | --- |
| `beamertheme-metropolis` | ベースのテーマ |
| `luatexja-fontspec` | LuaLaTeX での日本語・フォント設定 |
| `appendixnumberbeamer` | 付録を総ページ数から除外 |
| `xcolor` | 色指定 |
| `amsmath` | 数式 |
| `unicode-math` | OpenType 数式フォント |
| `graphicx` | 画像の挿入 |
| `here` | 図表の `[H]` 指定 |
| `putimg`（→ `textpos`） | `\putimage` |

`ka_metro.sty` 内にはコメントアウトした状態で `mathtools`, `physics`, `booktabs`, `multirow`, `subcaption` などの候補が並んでいるので、必要に応じて有効化するか、原稿側で読み込んでください。

---

## 注意点・既知の問題

- **`\bm` / `\boldsymbol` は使えません**（`unicode-math` のため）。太字の数式記号は `\symbf{x}` や `\mathbf{x}` を使ってください。
- `numbering=fraction` を指定した後に `\metroset{numbering=none}` で上書きしているため、`\metroset{numbering=...}` で右下のページ番号を戻すとタイトルバー右端の番号と二重に表示されます。

---

## 更新履歴

| バージョン | 内容 |
| --- | --- |
| v1.2 | `roundbox` の角丸を修正、強調色 `AlertColor` を追加し `\alert` と alertblock の配色に使用、ページ番号色の名前を `framenumcolor` に変更、`\putimage` の枠線表示を無効化、README をマニュアルとして整備 |
| v1.1 | `putimg.sty` を追加し、画像を割合指定で絶対配置する `\putimage` コマンドを実装 |
| v1.0 | 初版。metropolis をベースにフォント・配色・フレームタイトル・タイトルページ・セクションページ等をカスタマイズ |
