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
| 差分エンジン | `diff -d` コマンド | Myersアルゴリズム + cleanup後処理（`diff -d` 相当） |
| 結果の保存 | サーバ保存・公開URL発行 | ローカルHTMLファイル保存 |
| 入力上限 | 5MB | 片側40000トークン |
| 動作環境 | Perl/CGI/diff/FIFO | モダンブラウザ単体 |


使い方
------

- 公開版にアクセス: https://tools.nakaix.com/difff-js/
- またはこのリポジトリから `index.html` をダウンロードしてブラウザで開く
- 自前のサーバに設置する場合は、`index.html` を公開ディレクトリに置くだけでよく、CGIやサーバ側ソフトウェアの設定は不要です


技術メモ
--------

- トークン分割規則: オリジナル `split_text` と同一の正規表現 (`[a-z]+ | <$> | &#?\w+; | .`)
- 差分アルゴリズム: Myers diffアルゴリズム + cleanup後処理（`diff -d` 相当）。計算量 O((N+M)D)・メモリ O(N+M)（D は編集距離）
- 差分計算はWeb Worker内で実行。メインスレッドはブロックされず、計算中も中断可能
- HTML/CSS/JavaScript すべて単一ファイル内に展開


バージョン履歴
--------------

- **difff-js-0.02.html** (現行版・`index.html` と同内容)
    - 差分エンジンを LCS から Myers + cleanup（`diff -d` 相当）に変更
    - 入力上限を 5000 → 40000 トークンに拡張
    - 差分計算を Web Worker 化し、計算中も UI が応答するように
    - 計算中表示と中断ボタンを追加
- **difff-js-0.01.html** (初版)
    - LCS ベースの差分実装、片側 5000 トークン上限


ライセンス
----------

modified BSD license（オリジナルから継承）

- Original: Copyright &copy; 2004-2026 [@meso_cacase](https://twitter.com/meso_cacase)
- JS port: [@TadashiNakai](https://x.com/TadashiNakai)


不具合報告
----------

JS版固有の不具合は本リポジトリの Issues、もしくは [@TadashiNakai](https://x.com/TadashiNakai) (X) までお願いします。オリジナル difff の挙動に関するものは [meso-cacase/difff](https://github.com/meso-cacase/difff) 側にお問い合わせください。


English summary
---------------

**difff-js** is a single-file HTML port of [meso-cacase/difff](https://github.com/meso-cacase/difff), originally a Perl/CGI text comparison tool. All diff computation runs in the browser; no text is ever sent externally.

- Live: https://tools.nakaix.com/difff-js/
- Original: [difff《ﾃﾞｭﾌﾌ》](https://difff.jp/) by [@meso_cacase](https://twitter.com/meso_cacase)
- License: modified BSD (inherited from the original)
- JS port: [@TadashiNakai](https://x.com/TadashiNakai)

Just download `index.html` and open it in any modern browser. No installation, no server, no dependencies.

The diff engine uses the Myers algorithm with `diff -d`-style cleanup, runs in a Web Worker so the UI stays responsive, and accepts up to 40000 tokens per side.
