# Changelog

## [1.1.0] - 2026-09-25

### Added

- **Keyboard shortcuts** — every operation is now reachable without a mouse; press F1 to open the full shortcut list, which can also be navigated with the arrow keys and Enter
- **Send history** — Alt+↓ shows previously sent data; select one to put it into the send data field for editing before sending; Delete removes an entry; clear all available; kept separately for each communication mode
- **Send with Enter** — pressing Enter in the send data field sends the data
- **Keyboard scrolling in the log** — ↑ / ↓, PageUp / PageDown and Home / End scroll the log while it has the focus; Tab moves the focus to the log
- **Unsent-data confirmation** — disconnecting a serial connection while data is still waiting to be sent asks whether to discard it

### Improved

- **Settings** — options are grouped into Display, Communication, History and Behavior; "On battery" is now a drop-down select; select boxes are wider so long names such as theme names are no longer cut off; a "Send history count" option is added (1–100, default 20)
- **Settings button** — now shown as an icon; Ctrl+, opens settings
- **Toolbar tooltips** — all buttons show their keyboard shortcut in the tooltip
- **Focus ring** — the Connect, Text and Hex buttons, the log area and the color pickers in the settings show a focus ring when reached with Tab
- **Color pickers in the settings** — Enter opens the picker, Tab moves into the hex input field, and Tab again closes it and moves to the next option
- **Receive performance** — while data is arriving it is read continuously without waiting; the read buffer is larger; the terminator search only scans newly received data, so high-speed serial and TCP traffic is handled without falling behind

### Fixed

- After sending with nobody on the other side (for example, a com0com port whose pair is not open, or hardware flow control with CTS off), disconnecting left the serial port open, and reconnecting failed with "Access denied"
- Items in the settings panel extended past its right edge

---

## [1.0.0] - 2026-09-10

Initial release.

### Features

- UART (serial) and TCP communication (client / server)
- Send and receive text and binary data in the same session
- Mixed text/binary data support — send hex bytes alongside ASCII strings
- Text and hex display modes
- Copy log lines or selected text via Ctrl+C or right-click context menu
- Color-coded log — separate colors for sent and received data
- Status bar showing current connection settings at a glance
- Window position, size, and maximized state restored on next launch
- Language switching (English / Japanese) via the settings screen, extensible with additional language files
- Color themes — follow the OS light/dark setting, or custom themes via JSON files in the `themes` folder
- Adjustable scroll speed and acceleration, with automatic catch-up when data arrives faster than the view can scroll
- GPU-accelerated UI via GPUI (falls back to CPU rendering if no GPU is available)

---

# 更新履歴

## [1.1.0] - 2026-09-25

### 追加

- **キーボードショートカット** — すべての操作をマウスなしで行えるようになりました。F1 でショートカット一覧を開けます。一覧は矢印キーと Enter でも操作できます
- **送信履歴** — Alt+↓ で過去に送信したデータの一覧を表示します。選ぶと送信データ入力欄に入り、編集してから送信できます。Delete で個別削除、全削除にも対応しています。通信方式ごとに分けて記憶します
- **Enter で送信** — 送信データ入力欄で Enter を押すと送信します
- **ログのキー操作** — ログにフォーカスがあるとき、↑ / ↓・PageUp / PageDown・Home / End でスクロールできます。Tab でログへフォーカスを移せます
- **未送信データの確認** — シリアル通信で送信待ちのデータが残っているときに切断すると、破棄してよいかを確認します

### 改善

- **設定画面** — 項目を「表示」「通信」「履歴」「動作」に区分けしました。「バッテリー動作時」がドロップダウンになりました。セレクトボックスを広げ、配色名などの長い名前が切れないようにしました。「送信履歴数」を追加しました（1〜100、既定値 20）
- **設定ボタン** — アイコン表示にしました。Ctrl+, で設定を開けます
- **ツールチップ** — すべてのボタンにキーボードショートカットを表示するようにしました
- **フォーカス枠** — 「接続」「文字」「16進」ボタン、ログ表示領域、設定画面のカラーピッカーに、Tab で移ったときのフォーカス枠を表示するようにしました
- **設定画面のカラーピッカー** — Enter で開き、Tab で16進の入力欄へ移り、もう一度 Tab で閉じて次の項目へ移れるようにしました
- **受信性能** — データが届いている間は待たずに読み続けるようにしました。読み込みのバッファを大きくし、終端文字の検索を新しく届いた分だけにしたため、高速なシリアル通信・TCP通信でも取りこぼしにくくなりました

### 修正

- 相手がいない状態（例：com0com の相手側のポートが開かれていない、ハードウェアフロー制御で CTS が OFF のまま）で送信したあとに切断すると、シリアルポートが開いたままになり、再接続が「アクセスが拒否されました」で失敗する問題を修正しました
- 設定画面の項目が右端からはみ出していた問題を修正しました

---

## [1.0.0] - 2026-09-10

最初のリリース。

### 主な機能

- UARTシリアル通信・TCP通信（クライアント／サーバー）
- 文字列とバイナリデータの送受信
- 文字列とバイナリが混在するデータの送受信
- 文字表示・16進表示の切り替え
- ログの選択テキストまたは行単位でのコピー（Ctrl+C・右クリックメニュー）
- 送信データと受信データをそれぞれ個別に色設定できるカラーログ
- 現在の接続設定をひと目で確認できるステータスバー
- ウィンドウの位置・サイズ・最大化状態の復元
- 設定画面からの言語切り替え（英語・日本語。言語ファイルの追加で他の言語にも対応）
- カラーテーマ（OSの設定に追従、または `themes` フォルダへのJSON配置によるカスタムテーマ）
- スクロール速度と加減速度の調整。表示が追いつかないほどデータが届いた場合は自動的に速度を上げて回収
- GPUI によるGPUアクセラレーションUI（GPU未搭載環境ではCPU描画にフォールバック）