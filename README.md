difff-js — difff《ﾃﾞｭﾌﾌ》 JS版
==============================

[meso-cacase/difff](https://github.com/meso-cacase/difff) (Perl/CGI製テキスト比較ツール) を、依存ライブラリなしの単一HTMLファイルに移植したものです。差分計算はすべてブラウザ内で実行されるため、入力テキストは一切外部に送信されません。

**公開版: https://tools.nakaix.com/difff-js/**


特徴
----

- 単一HTMLファイル・依存ライブラリなし
- 差分計算はブラウザ内で完結（入力テキスト非送信）
- オリジナル ver.6.1 とほぼ機能互換（後述の違いを除く）
- 日本語 / English UI切替
- 結果のローカルHTMLファイル保存


主な機能
--------

- 2テキストの差分をハイライト表示（英単語は単語単位、日本語は1文字単位で比較）
- 文字数・空白数・改行数・単語数のカウント
- カラー切替（青／緑／モノクロ）
- 「結果のみ表示」印刷モード
- HTMLファイルとして保存 — 保存ファイルはそのまま再編集可能なdifff-js
- 日英バイリンガルUI
- 計算中はWeb Workerで別スレッド実行（UIが固まらず、中断ボタンで停止可能）


オリジナルとの違い
-----------------

| | オリジナル (Perl/CGI) | JS版 |
|---|---|---|
| 差分エンジン | `diff -d` コマンド（行単位） | GNU diffutils 由来のアルゴリズム（完全な diff -d） |
| 結果の保存 | サーバ保存・公開URL発行 | ローカルHTMLファイル保存 |
| 入力上限 | 5MB | 100000トークン超で確認ダイアログ |
| 動作環境 | Perl/CGI/diff/FIFO | モダンブラウザ単体 |


使い方
------

- 公開版にアクセス: https://tools.nakaix.com/difff-js/
- またはこのリポジトリから `index.html` をダウンロードしてブラウザで開く
- 自前のサーバに設置する場合は、`index.html` を公開ディレクトリに置くだけでよく、CGIやサーバ側ソフトウェアの設定は不要です


技術メモ
--------

- トークン分割規則: オリジナル `split_text` と同一の正規表現 (`[a-z]+ | <$> | &#?\w+; | .`)
- 差分アルゴリズム: GNU diffutils と同一のアルゴリズム（双方向Myers + shift_boundaries を移植）。完全な最小差分（diff -d）を保証
- 改行をまたぐ変更: `delete [NL]` / `insert [NL]` の際に行末・行頭へ空emタグを挿入することで、オリジナルの水色縦線表示を再現
- 差分計算はWeb Worker内で実行。メインスレッドはブロックされず、計算中も中断可能
- HTML/CSS/JavaScript すべて単一ファイル内に展開


バージョン履歴
--------------

- **difff-js-0.14.html** (現行版・`index.html` と同内容)
    - pageTitleへの「JS版」の追加
- **difff-js-0.13.html**
    - 差分エンジンを独自の近似処理から、GNU diffutils のアルゴリズムの移植版（完全な `diff -d`）へ差し替え
    - 差分結果のハイライト埋め込みロジックを本家 difff.pl と一致するよう改修
- **difff-js-0.11.html**
    - 改行をまたぐ変更の行末・行頭に空emタグを挿入し、オリジナルの水色縦線表示を再現
    - Web Worker のソースに `absorbKeepsAcrossNewlines` を含めるよう修正（計算が終わらなくなるバグを修正）
    - Worker内エラー時のユーザー通知を追加
- **difff-js-0.10.html**
    - ブラウザ保存で混入した残骸（古い差分結果・`file://` URL等）をHTMLから除去
- **difff-js-0.09.html**
    - 改行をまたぐ変更（A複数行→B1行など）の行対応を本家と一致させるよう buildRows を全面改修（保留キュー方式）
    - `absorbKeepsAcrossNewlines` を追加: 改行削除をまたいで保持されていた共通トークンを変更側に吸収
- **difff-js-0.08.html**
    - 「比較する」ボタンのカスタムスタイル（padding等）を削除し、本家のブラウザデフォルトに合わせた
- **difff-js-0.07.html**
    - 行末スペースの `<em>` 処理を本家と一致させる `mergeAdjacentEms` を改良（連続する em の結合と末尾 `&nbsp;` のem外化を2段階処理に）
    - 差分出力HTMLがサンプル全13行で本家と完全一致
- **difff-js-0.06.html**
    - テキストエリアのフォントを本家に合わせる（monospace指定を削除）
    - ヘッダ内 `&emsp;` を本家HTMLどおりに修正
    - 新着情報リストの全角スペースを本家ソース準拠に
- **difff-js-0.05.html**
    - 本家のCSS（`table`/`td` グローバルスタイル、`width:95%; margin:20px`）を精査して再現
    - body margin削除、テーブル幅・フォントサイズ等を本家と一致させた
- **difff-js-0.04.html**
    - 連続する変更トークン間のスペースが着色されない問題を修正（`mergeAdjacentEms` 追加）
    - 改行数カウントを本家と一致させる（末尾改行を加算）
- **difff-js-0.03.html**
    - 入力上限をエラーストップから警告ダイアログに変更（100000トークン超で確認）
- **difff-js-0.02.html**
    - 差分エンジンを LCS から Myers + cleanup（`diff -d` 相当）に変更
    - 入力上限を 5000 → 40000 トークンに拡張
    - 差分計算を Web Worker 化し、計算中も UI が応答するように
    - 計算中表示と中断ボタンを追加
- **difff-js-0.01.html** (初版)
    - LCS ベースの差分実装、片側 5000 トークン上限


ライセンス
----------

本ツール全体は **GNU General Public License v3.0 (GPLv3)** またはそれ以降のバージョンの下で配布されます。
内部には以下の異なるライセンスを持つコードが含まれています。

- **差分アルゴリズム部分 (GNU diffutils由来)**
  - License: GNU General Public License v3.0 (GPLv3)
  - Copyright (C) Free Software Foundation, Inc.
- **UIおよびベースロジック (オリジナル difff《ﾃﾞｭﾌﾌ》由来)**
  - License: modified BSD license
  - Original: Copyright &copy; 2004-2026 [@meso_cacase](https://twitter.com/meso_cacase)
- JS port: [@TadashiNakai](https://x.com/TadashiNakai)


不具合報告
----------

JS版固有の不具合は本リポジトリの Issues、もしくは [@TadashiNakai](https://x.com/TadashiNakai) (X) までお願いします。オリジナル difff の挙動に関するものは [meso-cacase/difff](https://github.com/meso-cacase/difff) までお問い合わせください。


English summary
---------------

**difff-js** is a single-file HTML port of [meso-cacase/difff](https://github.com/meso-cacase/difff), originally a Perl/CGI text comparison tool. All diff computation runs in the browser; no text is ever sent externally.

- Live: https://tools.nakaix.com/difff-js/
- Original: [difff《ﾃﾞｭﾌﾌ》](https://difff.jp/) by [@meso_cacase](https://twitter.com/meso_cacase)
- License: **GPLv3** (Core diff algorithm: GPLv3 / UI and base logic: modified BSD)
- JS port: [@TadashiNakai](https://x.com/TadashiNakai)

Just download `index.html` and open it in any modern browser. No installation, no server, no dependencies.

The diff engine is a JavaScript port of the GNU diffutils algorithm (exact diff -d), runs in a Web Worker so the UI stays responsive, and accepts up to 100,000 tokens per side before showing a confirmation dialog.
