# Reboku EPUB2md

![授權 / License](https://img.shields.io/badge/license-GPL--3.0-blue)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)

## 本工具：EPUB2md 轉換器 | This Tool: EPUB2md Converter

把 EPUB 電子書轉成一份**標準的 Markdown 檔**（CommonMark）：`#`/`##` 是章節、檔首的表頭寫書名作者系列、
封面內嵌在表頭裡、註解變成 Markdown 註解。任何 Markdown 工具都能打開；用支援本格式的閱讀器打開，
書名、作者、系列、階層目次、章節跳轉、封面、註解彈窗全部都在。單一 Python 檔、零必要相依、附命令列與圖形介面。

Convert EPUB books into a **standard Markdown file** (CommonMark): `#`/`##` are the chapters, a
front-matter header carries title / author / series, the cover is embedded in the header, and the
book's notes become Markdown footnotes. Any Markdown tool opens it; a reader that knows this format
gets the title, authors, series, nested contents, chapter jumps, cover and tap-to-read notes. One
Python file, no required dependencies, with both a command line and a window.

---

## 關於 Reboku App | About Reboku

**Reboku** 是 Android 上的個人 EPUB/CBZ 閱讀器，忠實解析與渲染電子書，支援繁體中文、簡體中文、日文、英文等多種語言。自有的 TypeScript 閱讀引擎 + 前處理管線，在 WebView 內完整運行，不依賴任何第三方閱讀器。

立即在 Google Play 下載：**[Reboku — 電子書閱讀器](https://play.google.com/store/apps/details?id=com.reboku.reader)**

**Reboku** is a personal EPUB/CBZ reader for Android. It features a custom TypeScript reading engine plus preprocessing pipeline running entirely inside a WebView, with no third-party reader dependencies. Full support for Traditional Chinese, Simplified Chinese, Japanese, English, and more.

Get it on Google Play now: **[Reboku — eBook Reader](https://play.google.com/store/apps/details?id=com.reboku.reader)**

---

## 目錄 | Contents

- [這是什麼 | What it is](#這是什麼--what-it-is)
- [安裝與執行 | Install and run](#安裝與執行--install-and-run)
- [命令列 | Command line](#命令列--command-line)
- [圖形介面 | The window](#圖形介面--the-window)
- [輸出格式 | The output format](#輸出格式--the-output-format)
  - [1. 表頭 | The header](#1-表頭--the-header)
  - [2. 章節 | Chapters](#2-章節--chapters)
  - [3. 段落、縮排與空行 | Paragraphs, indent and blank lines](#3-段落縮排與空行--paragraphs-indent-and-blank-lines)
  - [4. 章內的小標題 | Sub-headings inside a chapter](#4-章內的小標題--sub-headings-inside-a-chapter)
  - [5. 封面 | The cover](#5-封面--the-cover)
  - [6. 超連結與畫線 | Links and drawn rules](#6-超連結與畫線--links-and-drawn-rules)
  - [7. 註解 | Footnotes](#7-註解--footnotes)
  - [8. 逃脫 | Escaping](#8-逃脫--escaping)
  - [9. 編碼 | Encoding](#9-編碼--encoding)
- [範例檔 | The sample pair](#範例檔--the-sample-pair)
- [圖片型書籍的判定 | How picture books are detected](#圖片型書籍的判定--how-picture-books-are-detected)
- [程式架構 | Architecture](#程式架構--architecture)
- [測試 | Tests](#測試--tests)
- [授權 | License](#授權--license)

---

## 這是什麼 | What it is

一本 EPUB 是一包 zip：裡面有 OPF 清單、閱讀順序（spine）、目次（NCX 或 nav），以及一堆 XHTML。
這隻程式把那些讀出來，重新寫成一個 `.md`：書籍資訊寫在檔首的 `---` 表頭裡、章節用 `#` 標出層級、
封面以一行 `data:` 圖片夾在表頭、註解寫成 `[^標籤]`。結果用任何 Markdown 編輯器打開仍然是一本讀得下去的書；
用支援本格式的閱讀器打開，結構全部都在。

**每一份輸出都是合法的 CommonMark。** 本格式在標準之上加的東西（精確留白、加框小標題）都做成
「別的工具看了無害」的寫法：拿去 GitHub、Obsidian 或任何 Markdown 工具開，擴充的部分只是失去效果，不會壞掉。

An EPUB is a zip: an OPF manifest, a reading order (spine), a table of contents (NCX or nav) and
a pile of XHTML. This program reads those and writes one `.md`: book information in a `---`
front-matter header, chapters as `#` headings by level, the cover as one `data:` image line in
the header, notes as `[^label]` footnotes. Every file it writes is valid CommonMark; the few
extensions this format adds (exact blank lines, framed sub-headings) are harmless to any other
Markdown tool — they simply lose their effect there.

它與 [EPUB2txt](https://github.com/Dino9021/Reboku_EPUB2txt) 讀 EPUB 的方式完全相同（章節來自書自己的目次、
註解依 id 配對、小標題由樣式表判定、封面、編碼），只是寫出來的是 Markdown 而不是純文字。
It reads an EPUB exactly the way [EPUB2txt](https://github.com/Dino9021/Reboku_EPUB2txt) does
(chapters from the book's own contents, notes paired by id, sub-headings from the stylesheet,
cover, encoding); only the output differs.

---

## 安裝與執行 | Install and run

需要 Python 3.8 以上。

```bash
git clone https://github.com/Dino9021/Reboku_EPUB2md.git
cd Reboku_EPUB2md

# 可選：要夾帶封面才需要 / optional, only if you want covers embedded
pip install -r requirements.txt

python EPUB2md.py            # 開視窗 / opens the window
python EPUB2md.py book.epub  # 直接轉一本 / convert one book
```

沒裝 Pillow 也能跑，只是表頭不會有 `cover:` 那一行，程式會在命令列與狀態列各說一次原因。
Without Pillow everything still works; the header just has no `cover:` line, and the program
says why once.

---

## 命令列 | Command line

```
python EPUB2md.py [來源] [-o 輸出資料夾] [-r] [-f] [--flat] [--no-cover] [--cover-size WxH] [--cover-quality N]
```

| 參數 / Option | 作用 / What it does |
|---|---|
| `來源` / `source` | `.epub` 檔或一整個資料夾，自動判斷。**不給就開視窗。** An `.epub` file or a folder; omit to open the window. |
| `-o`, `--output` | 輸出資料夾。不給就寫在每本書旁邊。Output folder; default is beside each book. |
| `-r`, `--recurse` | 連子資料夾一起找。Also convert books in sub-folders. |
| `-f`, `--force` | 直接覆寫。不加時，遇到已存在的檔會一個一個問。Overwrite; otherwise it asks per file. |
| `--flat` | 全部 `.md` 直接放進 `-o`，不重建來源的子資料夾結構。Flat output, no sub-folders. |
| `--no-cover` | 不夾帶封面。No cover in the header. |
| `--cover-size WxH` | 封面等比例縮到這個框裡（預設 `200x300`，只縮不放）。Box the cover is fitted inside, keeping its ratio (default `200x300`). |
| `--cover-quality N` | 封面 JPEG 品質 1–95（預設 `80`）。JPEG quality of the cover, 1–95 (default `80`). |
| `--gui` | 帶了來源仍然開視窗。Open the window even with a source given. |
| `--self-test` | 跑內建檢查後結束。Run the built-in checks and exit. |

**輸出檔名一律與來源同名**：`book.epub` → `book.md`。
The output is always named after the source file.

```bash
# 一整個書庫，照原本的資料夾結構輸出到 D:\md
python EPUB2md.py C:\books -r -o D:\md

# 同上，但全部攤平成一層
python EPUB2md.py C:\books -r -o D:\md --flat

# 遇到已存在的檔一律覆寫，不要問
python EPUB2md.py C:\books -r -o D:\md -f
```

沒人在鍵盤前（管線、排程）而又沒加 `-f` 時，**一律保留既有檔**、絕不無聲覆寫，結尾會列出被保留的數量。
When nothing can answer the prompt (a pipe, a scheduled run) and `-f` was not given,
existing files are always kept — never silently overwritten.

---

## 圖形介面 | The window

不帶參數執行就開視窗：選來源（檔或資料夾）、選輸出資料夾、勾「連子資料夾」「覆寫」「夾帶封面」，
按開始。每一本書轉完就在清單裡回報結果；遇到已存在的檔會跳出對話框問（是／否／全部是／全部否）。
視窗記得上次的來源與輸出位置。介面有中英兩種，預設跟著作業系統的顯示語言，右上角可切換。

Run with no arguments to open the window: pick the source (file or folder), the output folder,
tick recurse / overwrite / embed cover, press Start. Each book reports into the list as it
finishes; an existing file raises a yes / no / yes-to-all / no-to-all dialog. The window remembers
the last source and output. Chinese and English interfaces, following the OS display language,
switchable in the top-right corner.

---

## 輸出格式 | The output format

下面的例子直接取自 [`samples/Sample.md`](samples/Sample.md) —— 那是把 [`samples/Sample.epub`](samples/Sample.epub)
丟給這隻程式轉出來的結果，你可以自己重跑一次比對（見[範例檔](#範例檔--the-sample-pair)）。

The example below is taken straight from [`samples/Sample.md`](samples/Sample.md), which is what
this program produces from [`samples/Sample.epub`](samples/Sample.epub) — rerun it and compare.

```markdown
---
title: Aesop's Fables: A Selection
author: Aesop
language: en
cover: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcU…
---

# Introduction

　　Aesop is an ancient teller of short tales, and this little book gathers ten of the most beloved ones.

　　In these pages you will meet clever foxes, proud lions, a patient tortoise, and a boastful hare. Each animal learns something the hard way.

# The Fox and the Grapes

　　One warm afternoon a hungry fox trotted through an orchard and spotted a bunch of ripe grapes hanging high on a vine.

　　…

　　Moral: It is easy to despise what you cannot have.
```

### 1. 表頭 | The header

檔案**第一行**是 `---`，再一行 `---` 結束；中間一行一個 `鍵: 值`，多位作者用 `- ` 清單。
鍵：`title` `author` `publisher` `date` `language` `series` `series_index` `cover`。沒有的欄位不寫。

The file **starts** with `---` and a second `---` closes the header; one `key: value` per line
between them, several authors as a `- ` list. Keys: `title` `author` `publisher` `date`
`language` `series` `series_index` `cover`. Absent fields are simply not written.

```markdown
---
title: 小公主
author:
- 法蘭西絲・霍森・柏納特
- 譯者名
publisher: 某某出版
date: 2026
language: zh-TW
series: 世界經典
series_index: 3
---
```

值只在必要時加引號（空值、或本身以引號開頭或結尾），引號內不做逃脫；讀的一方剝掉一層引號就是原文。
A value is quoted only when it must be (empty, or itself starting / ending with a quote); nothing
inside the quotes is escaped — a reader strips one outer pair and has the original text.

### 2. 章節 | Chapters

書的目次是唯一的章節來源：每一個目次項目成為一個 `#` 標題，`#` 的數量 = 目次層級（最深 `######`）。
書自己的目錄頁不寫（閱讀器由標題產生目次），與表頭重複的書名頁不寫。目次指到沒有文字的頁面（封面、插圖）時，
標籤往後落在下一個有文字的頁面，一個都不會少。

The book's own table of contents is the only source of chapters: each entry becomes a `#`
heading, the number of `#` = the TOC depth (down to `######`). The book's contents page is not
written (a Markdown reader builds the TOC from the headings), nor a title page that repeats the
header. A TOC entry pointing at a page with no text (a cover, a plate) lands on the next page that
has text — none is lost.

### 3. 段落、縮排與空行 | Paragraphs, indent and blank lines

一段一行，段與段之間**一個空行**（CommonMark 的段落規則）。每一段開頭兩個**全形空白**（U+3000），
所有語言都是：對 CommonMark 而言全形空白是普通文字，所以縮排照原樣顯示；半形四格會變成程式碼區塊，所以不能用。

One paragraph per line, **one blank line** between paragraphs (CommonMark's paragraph rule).
Every paragraph opens with two **ideographic spaces** (U+3000), in every language: to CommonMark
they are ordinary text, so the indent shows as an indent; four ASCII spaces would start a code
block, which is why they cannot be used.

原書留白照留：原書空 N 行的地方，輸出 N+1 個空行（多出的那一個是段落分隔），支援本格式的閱讀器顯示 N 行空白、最多 3 行；
其他 Markdown 工具把多個空行合併成一個，無害。

The book's own blank lines are kept: where the book left N empty paragraphs the output has N+1
blank lines (one of them is the paragraph separator); a reader that knows this format shows N
blank lines, at most 3; any other Markdown tool merges them into one — harmless.

### 4. 章內的小標題 | Sub-headings inside a chapter

章內的小標題（原書用標題元素、或樣式表把它設得更大更粗的第一行）寫成 `###`（章節層級再深一層，最淺 `###`，
這樣它不會被當成章節）。原書畫了框的小標題在標題行尾加 ` {.box}`：支援本格式的閱讀器畫出外框，其他工具只多印幾個字。

In-chapter sub-headings (a heading element, or a first line the stylesheet sets larger and
bolder) are written as `###` — one level below the chapter, never shallower than `###`, so they
never become chapters. A sub-heading the book framed gets ` {.box}` at the end of the line: a
reader that knows this format draws the frame, any other tool prints those few characters.

```markdown
### 小標題 {.box}
```

### 5. 封面 | The cover

表頭一行 `cover: data:image/jpeg;base64,…`：封面等比例縮到 200×300 以內的 JPEG（品質 80），整張 base64 寫在同一行。
這兩個是預設值：`--cover-size WxH` 改框的大小、`--cover-quality N` 改 JPEG 品質（1–95），視窗裡也有對應欄位。
沒裝 Pillow 或 `--no-cover` 時就沒有這一行。

One header line `cover: data:image/jpeg;base64,…`: the cover fitted inside 200×300 as a JPEG
(quality 80), the whole base64 on that one line. Those are the defaults: `--cover-size WxH`
changes the box and `--cover-quality N` the JPEG quality (1–95); the window has matching fields.
Without Pillow, or with `--no-cover`, the line is absent.

### 6. 超連結與畫線 | Links and drawn rules

書裡的外部網址寫成 `<https://…>`（CommonMark autolink），接在它所屬的文字後面；圖示連結自己一行；
文字本身就是網址時只寫一次；書內連結不寫；追蹤參數（`utm_*` 等）被清掉。
原書畫的線（`<hr>`、上下框線的區塊）寫成 `---`，前後各一個空行。

External addresses become `<https://…>` (a CommonMark autolink) after the text they belong to;
icon-only links get their own line; text that is itself the address is written once; in-book
links are not written; tracking parameters (`utm_*` and friends) are removed. A rule the book
drew (`<hr>`, a block with top and bottom borders) becomes `---` with a blank line each side.

### 7. 註解 | Footnotes

原書的註解（EPUB 3 / DPUB-ARIA 詞彙標出的，或參照與註解互指 id 的）寫成 Markdown 註解：內文 `[^2]`、
文末 `[^2]: 內容`。**標籤全書唯一**：書若每章重新編號，第二次出現的 2 寫成 `[^2-2]`。配對規則是「往後找第一個同標籤的註解」；
沒配到的記號、沒有人引用的註解都**保留原文**，書的字不會消失。支援本格式的閱讀器點記號跳出浮動視窗，標籤是回跳的連結。

The book's notes (marked with the EPUB 3 / DPUB-ARIA vocabulary, or a reference and a note
pointing at each other's id) become Markdown footnotes: `[^2]` in the text, `[^2]: …` at the
end. **Labels are unique across the book**: if the book restarts numbering per chapter the second
`2` becomes `[^2-2]`. Pairing is "the first note with the same label at or after the marker"; an
unpaired marker and an uncited note both **keep their text** — nothing of the book disappears.

### 8. 逃脫 | Escaping

書的內文裡對 CommonMark 有意義的標點 — `\` `*` `_` `` ` `` `[` `]` `<` `>` `~` `&` `|` — 一律加反斜線，
所以書裡的 `*`、`[1]`、`<b>` 都還是那幾個字，不會變成強調、連結或 HTML。行首的 `#`、`-`、`>`、`1.` 由全形縮排保護。
只有程式自己寫的網址與註解記號不逃脫。

Punctuation that means something to CommonMark inside prose — `\` `*` `_` `` ` `` `[` `]` `<` `>`
`~` `&` `|` — is backslash-escaped, so a book's own `*`, `[1]` or `<b>` stay those characters
instead of becoming emphasis, a link or HTML. `#`, `-`, `>`, `1.` at the start of a line are
protected by the indent. Only the addresses and note markers the program itself writes are left
unescaped.

### 9. 編碼 | Encoding

UTF-8，不帶 BOM，LF 換行。

UTF-8, no BOM, LF line endings.

---

## 範例檔 | The sample pair

[`samples/`](samples/) 裡有一組對照：

| 檔案 | 內容 |
|---|---|
| [`Sample.epub`](samples/Sample.epub) | 一本小小的 EPUB：《伊索寓言》選十則，含封面圖 |
| [`Sample.md`](samples/Sample.md) | **就是把上面那個檔丟給這隻程式轉出來的結果** |

```bash
python EPUB2md.py samples/Sample.epub -o /tmp/out
# 產出的 /tmp/out/Sample.md 會與 samples/Sample.md 完全一致（逐位元組）
```

兩個檔案都是為了測試而寫的伊索寓言改寫本，**沒有版權問題**，可以自由取用、修改、拿去當你自己的格式範本。

`samples/` holds a matched pair: a small EPUB (ten of Aesop's fables, with a cover) and exactly
what this program produces from it — byte-for-byte. Both files are a retelling written for
testing and carry **no copyright restrictions**; use or modify them freely.

---

## 圖片型書籍的判定 | How picture books are detected

漫畫與掃描書沒有文字可抽，轉出來會是一份幾乎空白的 `.md`。判定用計數不用猜：一頁只有一張圖、幾乎沒有字，
就是「單圖頁」；單圖頁佔七成以上的書是圖片書，會在開始前一次列出並略過。門檻是對真實書庫量出來的：
圖片書 99% 以上、文字書 7–18%、最極端的文字書（54 頁配 74 張插圖）37%，七成落在一段 60 個百分點的空檔中間。

A comic or a scanned book has no text to extract, so converting it would write an all-but-empty
`.md`. Telling one from a text book is counting, not guessing: a page holding exactly one picture
and almost no words is a "single-image page", and a book made mostly of those (70%+) is a picture
book, listed once up front and skipped. The thresholds were measured against a real library.

---

## 程式架構 | Architecture

單一檔案 `EPUB2md.py`，由上而下分成幾段，每段只依賴它上面的：讀 XHTML 的區塊抽取、EPUB 模型
（container.xml → OPF → 書籤資訊、manifest、spine、目次、封面）、樣式表判小標題、註解配對、
**Markdown 輸出層**（表頭、章節、段落與空行、註解標籤、逃脫）、工作模型（命令列與圖形介面共用同一層，
行為不可能不一致）、圖片書判定、命令列、視窗。

One file, in sections, each depending only on the ones above it: XHTML block extraction, the EPUB
model (container.xml → OPF → metadata, manifest, spine, TOC, cover), stylesheet-based sub-heading
detection, footnote pairing, the **Markdown emitter** (header, chapters, paragraphs and blank
lines, footnote labels, escaping), the job model shared by both front ends, picture-book
detection, the CLI, the window.

**沒有第三方相依**（Pillow 只在要封面時延遲載入），所以任何裝了 Python 3.8+ 的機器都跑得動。
No third-party dependency (Pillow is imported lazily, only for covers).

---

## 測試 | Tests

```bash
python EPUB2md.py --self-test
```

不需要任何測試框架，跑完印一行 `self-test OK`。檢查本身放在開發用的 `selftest.py`，**不在發行檔裡** ——
發行的就是 `EPUB2md.py` 這一個檔案；核心檢查是把 `samples/Sample.epub` 轉一次，輸出必須與 `samples/Sample.md`
**逐位元組相同**，再加上輸出層每一條規則（逃脫、表頭、空行對應、標題層級、註解配對與唯一標籤、橫線、加框小標題）的釘住案例。

No framework; it prints `self-test OK`. The checks live in a development-only `selftest.py`
(not part of a release — what ships is `EPUB2md.py` alone); the core check converts
`samples/Sample.epub` and requires the output to be **byte-identical** to `samples/Sample.md`,
plus pinned cases for every emitter rule (escaping, header, blank-line mapping, heading levels,
footnote pairing and unique labels, rules, framed sub-headings).

---

## 另一支轉檔工具 | The other converter

要**純文字書**（`.txt`，連記事本都打得開）而不是 Markdown，就用 **EPUB2txt**：
**[Reboku_EPUB2txt](https://github.com/Dino9021/Reboku_EPUB2txt)**。兩支讀 EPUB 的方式完全相同
（章節來自書自己的目次、註解依 id 配對、小標題由樣式表判定、封面、編碼），只差寫出來的格式。

Want a **plain-text book** (`.txt`, readable even in Notepad) instead of Markdown? Use
**EPUB2txt**: **[Reboku_EPUB2txt](https://github.com/Dino9021/Reboku_EPUB2txt)**. Both read an
EPUB exactly the same way (chapters from the book's own contents, notes paired by id,
sub-headings from the stylesheet, cover, encoding); only the output format differs.

---

## 授權 | License

GPL-3.0-or-later。完整條文見 [LICENSE](LICENSE)。

Copyright (C) 2026 Dino9021

This program is free software: you can redistribute it and/or modify it under the terms
of the GNU General Public License as published by the Free Software Foundation, either
version 3 of the License, or (at your option) any later version. It is distributed in
the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied
warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General
Public License for more details.
