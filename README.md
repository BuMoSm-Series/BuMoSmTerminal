# BuMoSm Terminal

A simple serial and TCP communication terminal for Windows.  
Built with [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) — GPU-accelerated rendering keeps CPU usage low, and falls back gracefully on systems without a dedicated GPU.

---

## Features

- UART (serial) and TCP communication (client / server)
- Send and receive text and binary data in the same session
- Mixed text/binary data support — send hex bytes alongside ASCII strings
- Text and hex display modes — switch between readable text and raw byte values
- Copy log lines or selected text via Ctrl+C or right-click context menu
- Color-coded log — configurable separate colors for sent and received data
- Status bar showing current connection settings at a glance
- Window position, size and maximized state restored on the next launch
- Language switching (English / Japanese) via the settings screen, extensible with additional language files
- Theme switching — follow the OS light/dark setting, or write your own theme as a JSON file
- Adjustable scroll speed and easing, with automatic catch-up when data arrives faster than the view can scroll
- GPU-accelerated UI via GPUI (falls back to CPU rendering if no GPU is available)
- Full keyboard operation — every operation can be done without a mouse; press F1 to open the shortcut list
- Per-mode send history — recall previously sent data, edit it and send it again

## Built With

- [Rust](https://www.rust-lang.org/) — systems programming language
- [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) — GPU-accelerated UI framework

## Requirements

- Windows 10 or later
- No additional runtime or GPU required

## Usage

### Keyboard shortcuts

| Action | Shortcut |
|---|---|
| Connect / disconnect | F5 |
| Go to the send data field | F6 |
| Send (in the send data field) | Enter |
| Open send history | Alt+↓ |
| Switch between text and hex display | Ctrl+M |
| Clear the log | Ctrl+L |
| Open settings | Ctrl+, |
| Open the keyboard shortcut list | F1 |
| Copy selection (log) | Ctrl+C |
| Select all (log) | Ctrl+A |
| Copy line (while the right-click menu is open) | Ctrl+Shift+C |
| Scroll one line up / down (log) | ↑ / ↓ |
| Scroll one page up / down (log) | PageUp / PageDown |
| Scroll to the top / end (log) | Home / End |

**Tab** and **Shift+Tab** move between buttons, the send data field and the log. The focused item is shown with a focus ring, and a focused button is pressed with **Enter** or **Space**. When the application starts, the communication mode select has the focus.

**F1** opens the shortcut list. Choose a row with the arrow keys and press **Enter**, or press the shortcut itself. Rows marked with `※` (sending, copy line and the log operations) only work in a specific place, so they are shown in the list but cannot be run from it.

While the settings panel is open, all shortcuts on the main window are disabled.

### Sending data

Type into the send data field and press **Enter**, or click **Send**. Data can be sent only while connected.

The send terminator configured in the settings is appended to each transmission.

**Disconnecting with unsent data (serial)**

If you disconnect while data is still waiting to be sent — for example, when hardware flow control holds transmission because the other side keeps CTS off — a confirmation appears. Choose **Yes** to discard the unsent data and disconnect, or **No** to stay connected and keep sending.

### Send history

The button between **Send** and the send data field lists the data you have sent. It can also be opened with **Alt+↓**.

- Choosing an entry puts it into the send data field without sending it, with the cursor at the end, so you can append to it before pressing **Enter**
- Use the × on a row, or **Delete**, to remove a single entry, and **Clear all** at the bottom of the list to remove them all
- Up and down move through the entries and **Clear all**; **Esc** closes the list

Only data that was actually sent is recorded. Sending the same data again moves it to the top instead of adding a duplicate. The send terminator is not included.

The history is kept separately for each communication mode (Serial, TCP Server, TCP Client) and stored in `config.json`, so it survives a restart. The number of entries kept is configurable in the settings (1–100, default 20); when the limit is exceeded, the oldest entries are removed.

### Sending mixed text and binary data

Wrap hex bytes in `{}` with space-separated 2-digit hex values:

```
Hello{0D 0A}
{41 42 43}
```

- Plain text outside `{}` is sent as UTF-8
- `{0D 0A}` is interpreted as raw bytes (e.g. CR LF)
- `{{` and `}}` are escape sequences for literal `{` and `}`

### Display modes

Click **Text** or **Hex**, or press **Ctrl+M**, to switch the log display:

- **Text** — received bytes are decoded as UTF-8. Bytes that cannot form valid UTF-8 are shown as `�` (U+FFFD). Each entry ends with `↵` for a normal terminator, or `│` for a timeout.
- **Hex** — all bytes are shown as space-separated 2-digit hex values (e.g. `41 42 43 0D 0A`).

Switching between the two modes clears the current text selection. The rendered text differs between modes, so a selection made in one mode would not cover the same data in the other.

### Copying log data

- **Ctrl+A** — selects every line currently held in the log
- **Ctrl+C** — copies the selected text in the log area
- **Right-click** — opens a context menu with "Copy Selection" and "Copy Line". While the menu is open, **Ctrl+Shift+C** copies the line you right-clicked

While dragging to select text, moving the cursor outside the log area scrolls the view automatically, so a selection can be extended beyond what is currently visible.

While the log has the focus, it can also be scrolled with the keyboard: **↑ / ↓** one line, **PageUp / PageDown** one page, **Home / End** to the top or end.

### Log capacity

The log retains up to **1,000 lines**. When this limit is reached, the oldest lines are removed automatically to make room for new ones.

### Window position and size

The window position, size and maximized state are saved when the application is closed, and restored the next time it starts. The monitor the window was displayed on is recorded as well, so on a multi-monitor setup the window reopens on the same monitor.

If that monitor is no longer connected, or the saved position no longer fits on the screen, the window opens at the default position on the primary monitor.

The state is written when the window is closed normally. It is not saved if the process is terminated forcibly, for example from Task Manager.

### Settings

Click the settings (gear) button at the right end of the toolbar, or press **Ctrl+,**, to open the settings panel. Settings cannot be changed while connected or listening.

The panel is divided into four groups: **Display**, **Communication**, **History** and **Behavior**. The options in the Communication group depend on the communication mode:

**Serial**

| Setting | Description |
|---|---|
| COM Port | Serial port to use |
| Baud Rate | Communication speed (bps) |
| Parity | None / Odd / Even |
| Stop Bits | 1 / 2 |
| Data Bits | Data length in bits |
| Flow Control | None / Software (XON/XOFF) / Hardware (RTS/CTS) |
| Timeout (ms) | Receive timeout. A line is finalized when no new data arrives within this period |
| Send Terminator | Bytes appended to each transmission (None / CR / LF / CR+LF / ETX / EOT) |
| Recv Terminator | Byte sequence that marks the end of a received line |

**Common to all modes**

| Group | Setting | Description |
|---|---|---|
| Display | Language | Display language |
| Display | Theme | Color scheme (see [Theme](#theme)) |
| Display | Send Color | Log color for sent data |
| Display | Recv Color | Log color for received data |
| History | Send history count | Number of send history entries kept per communication mode (1–100, default 20) |
| Behavior | Scroll Speed | How long one wheel notch takes to scroll (see [Scrolling](#scrolling)) |
| Behavior | Scroll Easing | How long the movement takes to speed up and slow down |
| Behavior | On battery | **Normal FPS** or **Reduce FPS**. Reduce FPS caps the frame rate at 30 FPS while running on battery |

**TCP Server**

| Setting | Description |
|---|---|
| Bind IP | IP address to listen on (`0.0.0.0` listens on all interfaces) |
| Listen Port | Port number to accept connections on |
| Timeout (ms) | Receive timeout |
| Send / Recv Terminator | Same as serial |

The server accepts **one client at a time**. After a client disconnects, it automatically resumes listening for the next connection.

**TCP Client**

| Setting | Description |
|---|---|
| Host | IP address or hostname of the server |
| Port | Port number to connect to |
| Connect Timeout (ms) | Maximum time to wait for a connection to be established |
| Timeout (ms) | Receive timeout |
| Send / Recv Terminator | Same as serial |

### Receive timeout

When a terminator is configured, a line is finalized when the terminator byte sequence is received.  
When no terminator is configured, or when partial data arrives without a terminator, the line is finalized after the configured timeout period with no new data. Lines finalized by timeout are marked with `│` instead of `↵`.

### Scrolling

Two settings control how the log scrolls. Both apply to the mouse wheel and to the automatic scrolling that follows incoming data.

**Scroll Speed (1-10, default 5)**

How long one wheel notch takes to travel its full distance. A larger number scrolls **faster**.

| Value | Duration |
|---|---|
| 1 | approx. 467 ms |
| 3 | approx. 367 ms |
| 5 | approx. 267 ms |
| 7 | approx. 167 ms |
| 10 | approx. 17 ms (near-instant) |

**Scroll Easing (1-5, default 3)**

How long the movement takes to speed up at the start and slow down at the end. A larger number gives a **sharper** start and stop, matching the direction of the speed setting.

| Value | Duration |
|---|---|
| 1 | approx. 83 ms |
| 3 | approx. 50 ms |
| 5 | approx. 17 ms |

When Scroll Speed is set to 8 or above, the total duration is too short to fit a gradual start, so easing is not applied. The movement starts at full speed and eases out as it approaches its destination.

**Catching up during heavy traffic**

When data arrives faster than the view can scroll, the backlog is absorbed by scrolling faster rather than by jumping. Once the backlog grows beyond about 60 lines, the scroll speed rises in proportion to how far behind the view is, so a larger backlog is cleared more quickly. As the backlog shrinks the speed falls with it, easing back to normal without a visible jump.

The colors of sent and received data are set separately under Send Color and Recv Color, and are not affected by the theme.

### Theme

The color scheme is selected under **Theme** in the settings panel. The list is split into two groups:

- **Standard** — the built-in light and dark schemes, plus **Follow OS**, which tracks the system light/dark setting
- **Theme Files** — schemes loaded from the `themes` folder next to the executable

Themes in the second group are plain JSON files. Editing one takes effect immediately, without restarting the application. See `THEME_README_en.txt` in the `themes` folder for the file format and the list of available color keys.

### Settings file

Settings are saved automatically to `config.json` in the same folder as the executable. The window position, size, maximized state and the monitor in use are stored in the same file. You can copy or back up this file to preserve your configuration.

### Language

The display language can be changed in the settings panel. Currently supported: **English** and **Japanese**.

The language strings are defined in `en.json` and `ja.json` in the `locales` folder next to the executable. You can edit these files to customize the wording.

Languages are read from that folder at startup, so you can add one by copying an existing file, renaming it to the language code you want (`fr.json`, `de.json` and so on) and translating its contents. The new language appears in the settings panel the next time the application starts. The name shown in the list comes from the `locale.language_name` entry inside the file.

### Multiple instances

Only one instance of BuMoSm Terminal can run at a time. If you launch a second instance, the existing window is brought to the front and the new instance exits immediately.

## License

Copyright 2026 nabehiro  
Licensed under the [Apache License, Version 2.0](LICENSE).

This software is provided "as is", without warranty of any kind.  
The author is not responsible for any damages or issues arising from its use.

## Contributing / Bug Reports

If you find a bug or have a question, please open an [Issue](https://github.com/BuMoSm-Series/BuMoSmTerminal/issues).

## Acknowledgements

- [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) — Apache 2.0
- [gpui-component](https://github.com/longbridge/gpui-component) — Apache 2.0

---

# BuMoSm Terminal（日本語）

Windows向けのシンプルなシリアル・TCP通信ターミナルです。  
[GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) によるGPU描画でCPU負荷を低減します。GPU未搭載の環境でもCPU描画にフォールバックして動作します。

---

## 機能

- UARTシリアル通信・TCP通信（クライアント／サーバー）に対応
- 文字列とバイナリデータの送受信が可能
- 文字列とバイナリが混在するデータの送受信に対応
- 文字表示・16進表示の切り替えが可能
- ログの選択テキストまたは行単位でのコピー（Ctrl+C・右クリックメニュー）
- 送信データと受信データをそれぞれ個別に色設定できるカラーログ
- 現在の接続設定をひと目で確認できるステータスバー
- 終了時のウィンドウ位置・サイズ・最大化状態を次回起動時に復元
- 設定画面から表示言語を切り替え可能（日本語・英語。言語ファイルを追加すれば他の言語も追加可能）
- 配色の切り替えに対応（OSの設定に追従、またはJSONファイルで自作）
- スクロールの速度・加減速度を調整可能。表示が追いつかないほどデータが届いた場合は自動的に速度を上げて回収
- GPUIによるGPU描画（GPU未搭載の場合はCPU描画で動作）
- すべての操作をキーボードだけで実行可能（F1 でキーの一覧を表示）
- 通信方式ごとの送信履歴 — 送信したデータを呼び出し、編集して再送信

## 開発言語・フレームワーク

- [Rust](https://www.rust-lang.org/) — システムプログラミング言語
- [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) — GPU描画UIフレームワーク

## 動作環境

- Windows 10 以降
- 追加のランタイムやGPUは不要

## 使い方

### キー操作

| 操作 | キー |
|---|---|
| 接続・切断 | F5 |
| 送信データ入力欄へ移る | F6 |
| 送信（送信データ入力欄で） | Enter |
| 送信履歴を開く | Alt+↓ |
| 文字表示と16進表示の切り替え | Ctrl+M |
| ログを消去 | Ctrl+L |
| 設定を開く | Ctrl+, |
| キーの一覧を開く | F1 |
| 選択部をコピー（ログ） | Ctrl+C |
| すべて選択（ログ） | Ctrl+A |
| 行をコピー（右クリックメニューを開いているとき） | Ctrl+Shift+C |
| 1行上へ・1行下へ（ログ） | ↑ / ↓ |
| 1画面上へ・1画面下へ（ログ） | PageUp / PageDown |
| 先頭へ・末尾へ（ログ） | Home / End |

**Tab**・**Shift+Tab** でボタン・送信データ入力欄・ログの間を移動できます。フォーカスのある場所には枠が表示され、ボタンは **Enter** または **Space** で押せます。起動直後は通信方式の選択にフォーカスがあります。

**F1** でキーの一覧を開きます。矢印キーで選んで **Enter** を押すか、そのキーを直接押すと実行できます。`※` の付いた行（送信・行をコピー・ログの操作）は決まった場所でだけ使える操作のため、一覧には表示しますが、一覧からは実行できません。

設定パネルを開いている間は、メイン画面のキー操作はすべて無効になります。

### 送信

送信データ入力欄に入力して **Enter** を押すか、**送信**ボタンをクリックします。送信できるのは接続中だけです。

送信するデータの末尾には、設定の送信終端が付加されます。

**未送信のデータがあるときの切断（シリアル通信）**

送信待ちのデータが残っている状態で切断しようとすると（例：ハードウェアフロー制御で相手が CTS を OFF にしたまま送信が止まっているとき）、確認のメッセージが表示されます。**はい**で未送信のデータを破棄して切断し、**いいえ**で接続したまま送信を続けます。

### 送信履歴

**送信**ボタンと送信データ入力欄の間のボタンで、送信したデータの一覧を開きます。**Alt+↓** でも開けます。

- 一覧から選ぶと、その内容が送信データ入力欄に入ります（送信はしません）。カーソルは末尾に置かれるので、追記してから **Enter** で送信できます
- 行の × または **Delete** で1件消せます。一覧の下の「すべて消す」ですべて消せます
- 上下キーで「すべて消す」を含む一覧を移動し、**Esc** で閉じます

記憶するのは実際に送信できたデータだけです。同じデータを送信した場合は、重ねて記憶せずに一番上へ移ります。送信終端は含みません。

送信履歴は通信方式（シリアル通信・TCPサーバ・TCPクライアント）ごとに分けて `config.json` に保存され、再起動後も残ります。記憶する件数は設定で変更できます（1〜100、既定値 20）。件数を超えると古いものから消えます。

### 文字列とバイナリを混在して送信する

`{}`で囲んだ中に16進数2桁をスペース区切りで記述します。

```
Hello{0D 0A}
{41 42 43}
```

- `{}`の外のテキストはそのままUTF-8で送信されます
- `{0D 0A}` のように`{}`内はバイト値として解釈されます（例：CR LF）
- `{{`と`}}`はそれぞれ`{`と`}`のエスケープ表記です

### 表示モード

**文字**・**16進**ボタン、または **Ctrl+M** で表示を切り替えられます。

- **文字** — 受信バイト列をUTF-8として表示します。有効なUTF-8に変換できないバイトは `�`（U+FFFD）で表示されます。各行の末尾は終端文字による区切りの場合 `↵`、タイムアウトによる区切りの場合 `│` が付きます。
- **16進** — すべてのバイトを16進数2桁のスペース区切りで表示します（例：`41 42 43 0D 0A`）。

表示モードを切り替えると選択範囲は解除されます。モードによって表示される文字列が異なるため、一方で選択した範囲がもう一方では同じデータを指さなくなるためです。

### ログのコピー

- **Ctrl+A** — ログに保持されている全行を選択します
- **Ctrl+C** — ログエリアで選択したテキストをコピーします
- **右クリック** — 「選択部をコピー」「行をコピー」のコンテキストメニューが表示されます。メニューを開いている間は **Ctrl+Shift+C** で右クリックした行をコピーできます

ドラッグで範囲選択している間にマウスカーソルをログ表示エリアの外へ動かすと、表示が自動的にスクロールします。画面に見えていない範囲まで続けて選択できます。

ログにフォーカスがあるときは、キーでもスクロールできます。**↑ / ↓** で1行、**PageUp / PageDown** で1画面、**Home / End** で先頭・末尾へ移動します。

### ログの最大行数

ログは最大 **1,000行** まで保持されます。上限に達すると、新しいデータが来るたびに古い行から順に削除されます。

### ウィンドウの位置とサイズ

終了時のウィンドウ位置・サイズ・最大化状態を保存し、次回起動時に復元します。表示していたモニターも記録するため、マルチモニター環境でも同じモニターに復元されます。

該当のモニターが接続されていない場合や、保存された位置が画面に収まらない場合は、プライマリモニターの既定位置に表示します。

保存はウィンドウを通常の操作で閉じたときに行われます。タスクマネージャーからの強制終了などでは保存されません。

### 設定

ツールバー右端の設定（歯車）ボタンをクリックするか、**Ctrl+,** を押すと設定パネルが開きます。接続中・待受中は設定を変更できません。

設定パネルは **表示**・**通信**・**履歴**・**動作** の4つの区分けに分かれています。「通信」に表示される項目は通信方式によって異なります。

**シリアル通信**

| 設定項目 | 説明 |
|---|---|
| COMポート | 使用するシリアルポート |
| 通信速度 | 通信速度（bps） |
| パリティ | None / Odd / Even |
| ストップビット | 1 / 2 |
| データ長 | データのビット長 |
| フロー制御 | None / Software (XON/XOFF) / Hardware (RTS/CTS) |
| タイムアウト(ms) | 受信タイムアウト。最後の受信からこの時間が経過すると行を確定します |
| 送信終端 | 送信データの末尾に付加するバイト列（None / CR / LF / CR+LF / ETX / EOT） |
| 受信終端 | 受信行の区切りを示すバイト列 |

**全モード共通**

| 区分け | 設定項目 | 説明 |
|---|---|---|
| 表示 | 言語 | 表示言語 |
| 表示 | 配色 | 画面の配色（[配色](#配色)を参照） |
| 表示 | 送信色 | 送信データのログ表示色 |
| 表示 | 受信色 | 受信データのログ表示色 |
| 履歴 | 送信履歴数 | 通信方式ごとに記憶する送信履歴の件数（1〜100、既定値 20） |
| 動作 | スクロール速度 | ホイール1ノッチ分を動かしきるまでの時間（[スクロール](#スクロール)を参照） |
| 動作 | スクロール加減速度 | 動き出しと止まり際にかける時間 |
| 動作 | バッテリー動作時 | **FPSを下げない**／**FPSを下げる**。FPSを下げるを選ぶと、バッテリー動作中はフレームレートを30FPSに抑えます |

**TCPサーバ**

| 設定項目 | 説明 |
|---|---|
| 待ち受けIP | 待ち受けに使用するIPアドレス（`0.0.0.0` は全インタフェースで待ち受け） |
| 待ち受けポート | クライアントの接続を受け付けるポート番号 |
| タイムアウト(ms) | 受信タイムアウト |
| 送信終端・受信終端 | シリアルと同様 |

同時接続は **1台のみ**です。クライアントが切断すると自動的に次の接続の待ち受けを再開します。

**TCPクライアント**

| 設定項目 | 説明 |
|---|---|
| 接続先ホスト | 接続先のIPアドレスまたはホスト名 |
| 接続先ポート | 接続先のポート番号 |
| 接続タイムアウト(ms) | 接続が確立するまでの最大待ち時間 |
| タイムアウト(ms) | 受信タイムアウト |
| 送信終端・受信終端 | シリアルと同様 |

### 受信タイムアウト

終端文字が設定されている場合、終端バイト列を受信した時点で行を確定します。  
終端文字が設定されていない場合、または終端文字が来ないまま一定時間が経過した場合、設定したタイムアウト時間が経過した時点で行を確定します。タイムアウトで確定した行は末尾に `↵` の代わりに `│` が表示されます。

### スクロール

スクロールの動きは2つの設定で決まります。どちらもマウスホイールと、受信データに追従する自動スクロールの両方に効きます。

**スクロール速度（1〜10、既定5）**

ホイール1ノッチ分を動かしきるまでの時間です。数字が大きいほど**速く**流れます。

| 設定値 | 所要時間 |
|---|---|
| 1 | 約467ms |
| 3 | 約367ms |
| 5 | 約267ms |
| 7 | 約167ms |
| 10 | 約17ms（ほぼ即座） |

**スクロール加減速度（1〜5、既定3）**

動き出しと止まり際にかける時間です。速度の設定と向きを揃えてあり、数字が大きいほど**きびきび**と動き出して止まります。

| 設定値 | 所要時間 |
|---|---|
| 1 | 約83ms |
| 3 | 約50ms |
| 5 | 約17ms |

スクロール速度を8以上にすると、全体の時間が短すぎてゆるやかな動き出しが入りません。この場合は加減速が適用されず、最初が最も速く、目標に近づくほど緩む動きになります。

**大量受信時の追いつき動作**

表示が追いつかないほど速くデータが届いた場合、一気に飛ぶのではなく、スクロールを速めることで遅れを回収します。未表示の遅れがおよそ60行を超えると、遅れの大きさに比例して速度が上がるため、溜まっているほど速く流れます。遅れが減るにつれて速度も落ちるので、飛ぶことなく通常の動きへ戻ります。

送信データと受信データの文字色は「送信色」「受信色」で個別に設定するもので、配色の影響を受けません。

### 配色

設定パネルの**配色**で画面の色を選べます。一覧は2つに分かれています。

- **標準** — 内蔵のライト・ダークに加え、OSのライト／ダーク設定に追従する**OSの設定に従う**
- **テーマファイル** — 実行ファイルと同じ場所にある `themes` フォルダから読み込んだ配色

後者はJSONファイルです。編集するとアプリを再起動しなくてもその場で反映されます。ファイルの書き方と指定できる色の一覧は、`themes` フォルダの `THEME_README_ja.txt` を参照してください。

### 設定ファイル

設定は実行ファイルと同じフォルダの `config.json` に自動保存されます。ウィンドウの位置・サイズ・最大化状態・表示していたモニターも同じファイルに保存されます。このファイルをコピーまたはバックアップすることで設定を保持できます。

### 言語設定

設定パネルから表示言語を切り替えられます。現在対応している言語は**日本語**と**英語**です。

表示文字列は実行ファイルと同じ場所にある `locales` フォルダの `ja.json`（日本語）と `en.json`（英語）で定義されています。これらのファイルを編集することで文言を変更できます。

言語は起動時にこのフォルダを読んで集めるため、ファイルを足せば言語を増やせます。既存のファイルをコピーして、ファイル名を追加したい言語コード（`fr.json`、`de.json` など）に変え、中身を翻訳してください。次回起動時から設定パネルの一覧に表示されます。一覧に出る名前は、ファイル内の `locale.language_name` の値です。

### 多重起動の禁止

BuMoSm Terminalは同時に1つのインスタンスしか起動できません。2つ目を起動しようとすると、既存のウィンドウが前面に表示されて新しいインスタンスは即座に終了します。

## ライセンス

Copyright 2026 nabehiro  
[Apache License, Version 2.0](LICENSE) のもとで公開しています。

本ソフトウェアは現状のまま（"as is"）提供されます。  
使用によって生じたいかなる損害・問題についても、作者は責任を負いません。

## バグ報告・質問

バグの報告や質問は [Issues](https://github.com/BuMoSm-Series/BuMoSmTerminal/issues) からお願いします。

## 使用ライブラリ

- [GPUI](https://github.com/zed-industries/zed/tree/main/crates/gpui) — Apache 2.0
- [gpui-component](https://github.com/longbridge/gpui-component) — Apache 2.0