# CLine46 キーマップ変更ガイド

このリポジトリは公開される前提で、CLine46 の ZMK 設定を管理しています。キー配列を変更するときは、基本的に `config/CLine46.keymap` を編集します。

## フォルダ構成

| パス | 役割 |
| --- | --- |
| `config/CLine46.keymap` | キーマップ本体。通常のキー変更はここを編集します。 |
| `config/CLine46.json` | keymap-drawer や Studio が使う物理レイアウト情報です。キー数や並びを変えない限り通常は触りません。 |
| `keymap-drawer/CLine46.yaml` | 配列図の元データです。図も更新したい場合に編集または生成します。 |
| `keymap-drawer/CLine46.svg` | 配列図の出力ファイルです。 |
| `boards/shields/CLine46/` | CLine46 のハードウェア定義です。配線、左右分割、トラックボール、バッテリーなどの設定が入っています。キー変更だけなら通常は触りません。 |
| `build.yaml` | GitHub Actions でビルドする対象です。右手、左手、settings_reset を生成します。 |
| `.github/workflows/build.yml` | push、pull request、手動実行で ZMK のビルドを行います。 |
| `.github/workflows/draw.yml` | 手動実行でキーマップ図を生成します。 |
| `firmware/` | 生成済み UF2 ファイルの保管場所です。過去の成果物なので、キー変更後は Actions の新しい Artifact を確認します。 |

## 公開リポジトリでの注意

このリポジトリには公開してよい設定だけを置きます。キー配列、ボード定義、ビルド設定は公開して問題ない情報ですが、次のような情報はコミットしないでください。

- 個人用のトークン、API キー、パスワード
- PC 名、ユーザー名、ローカルの絶対パスなど、公開したくない環境情報
- Bluetooth のペアリング済みデバイス情報や個人端末を特定できるメモ
- 公開したくない未配布ファームウェアや個人向け設定

## キーの並び

`config/CLine46.keymap` の各レイヤーは、物理キーの左上から右下へ、次の順番で 46 個の `bindings` が並びます。`combos` の `key-positions` もこの番号を使います。

```text
 0   1   2   3   4   5            6   7   8   9  10  11
12  13  14  15  16  17           18  19  20  21  22  23
24  25  26  27  28  29           30  31  32  33  34  35
    36  37  38  39  40  41   42  43          44  45
```

例えばデフォルトレイヤーの先頭は次のようになっています。

```dts
default_layer {
    bindings = <
&kp TAB      &kp Q     &kp W      &kp E     &kp R                &kp T                                            &kp Y     &kp U   &kp I       &kp O       &kp P       &kp LEFT_BRACKET
...
    >;
};
```

この例では、位置 0 が `TAB`、位置 1 が `Q`、位置 11 が `LEFT_BRACKET` です。

## 現在のレイヤー

`config/CLine46.keymap` では次のレイヤーが定義されています。

| レイヤー番号 | 名前 | 役割 |
| --- | --- | --- |
| 0 | `default_layer` | 通常入力 |
| 1 | `layer_1` | 数字、記号 |
| 2 | `MOUSE` | ファンクションキー、カーソル、クリック、ショートカット |
| 3 | `SCROLL` | Bluetooth 選択、Studio unlock、トラックボールのスクロール |
| 4 | `layer_4` | 予備 |
| 5 | `layer_5` | 予備 |
| 6 | `layer_6` | 予備 |

デフォルトレイヤーでは、次のキーで別レイヤーへ移動します。

- `&lt 1 SPACE`: タップで Space、長押しでレイヤー 1
- `&lt 2 INT_MUHENKAN`: タップで無変換、長押しで MOUSE レイヤー
- `&mo 3`: 押している間だけ SCROLL レイヤー

## よく使う書き方

| 書き方 | 意味 |
| --- | --- |
| `&kp A` | `A` を入力します。通常キーはこの形です。 |
| `&kp TAB` | Tab を入力します。 |
| `&kp RET` | Enter を入力します。 |
| `&kp BSPC` | Backspace を入力します。 |
| `&kp DEL` | Delete を入力します。 |
| `&kp LEFT_ARROW` | 左矢印を入力します。 |
| `&kp LC(C)` | Ctrl+C を入力します。`LC` は Left Control です。 |
| `&kp LS(TAB)` | Shift+Tab を入力します。`LS` は Left Shift です。 |
| `&mo 3` | 押している間だけレイヤー 3 に移動します。 |
| `&lt 1 SPACE` | タップで Space、長押しでレイヤー 1 に移動します。 |
| `&mt LSFT INT_HENKAN` | タップで変換、長押しで Shift として動きます。 |
| `&trans` | 下位レイヤーの同じ位置のキーをそのまま使います。 |
| `&bt BT_SEL 0` | Bluetooth プロファイル 0 を選択します。 |
| `&bt BT_CLR` | 現在の Bluetooth プロファイルのペアリングを削除します。 |
| `&mkp MB1` | マウスの左クリックです。 |

キー名は ZMK の keycode 名を使います。このリポジトリでは `#include <dt-bindings/zmk/keys.h>` を読み込んでいるため、`A`、`NUMBER_1`、`LEFT_ARROW`、`INT_HENKAN` などの名前を使えます。

## キーを変更する手順

1. `config/CLine46.keymap` を開きます。
2. 変更したいレイヤーを探します。
3. 上のキー位置表で変更したい物理キーの番号を確認します。
4. その位置にある `bindings` を差し替えます。
5. 1 つのレイヤーにつき、`bindings` の数が 46 個のままになっているか確認します。
6. 変更を push して GitHub Actions の build を実行します。
7. Actions の Artifact から右手用と左手用の UF2 を取得して書き込みます。

例として、デフォルトレイヤーの位置 0 を Tab から Esc に変える場合は、次のように変更します。

```diff
-&kp TAB      &kp Q
+&kp ESC      &kp Q
```

## レイヤーキーを変更する例

デフォルトレイヤーの `&lt 1 SPACE` は、タップで Space、長押しでレイヤー 1 です。長押し先を MOUSE レイヤーに変える場合は、レイヤー番号を `2` にします。

```diff
-&lt 1 SPACE
+&lt 2 SPACE
```

タップ動作を Space から Enter に変える場合は、後ろのキー名を変えます。

```diff
-&lt 1 SPACE
+&lt 1 RET
```

## コンボを変更する場合

`combos` は同時押しで別のキーを出す設定です。現在は次の 2 つがあります。

```dts
tab {
    bindings = <&kp TAB>;
    key-positions = <11 12>;
};

shift_tab {
    bindings = <&kp LS(TAB)>;
    key-positions = <12 13>;
};
```

`key-positions` は上のキー位置番号です。例えば `key-positions = <12 13>;` は、位置 12 と 13 の同時押しを意味します。

コンボを変更するときは、次の点に注意してください。

- `key-positions` は 0 始まりです。
- 押しやすく、誤って同時押ししにくい組み合わせにします。
- 既存の通常入力やレイヤーキーと誤爆しない組み合わせにします。

## キーマップ図を更新する場合

配列図は `keymap-drawer` で管理されています。

- 元データ: `keymap-drawer/CLine46.yaml`
- 出力: `keymap-drawer/CLine46.svg`
- 手動実行ワークフロー: `.github/workflows/draw.yml`

キーマップだけを変えた場合、ファームウェアの動作に必要なのは `config/CLine46.keymap` です。ただし、README や公開ページで図も見せたい場合は、`keymap-drawer/CLine46.yaml` と `keymap-drawer/CLine46.svg` も更新します。

## ビルド結果

`.github/workflows/build.yml` は ZMK の user config build を呼び出します。`build.yaml` では次の 3 つを生成対象にしています。

- `seeeduino_xiao_ble` + `CLine46_R rgbled_adapter`
- `seeeduino_xiao_ble` + `CLine46_L rgbled_adapter`
- `seeeduino_xiao_ble` + `settings_reset`

キー配列を変更したあとに通常使うのは、右手用と左手用の UF2 です。ペアリング情報を初期化したい場合だけ `settings_reset` を使います。

## 触らなくてよいファイル

通常のキー変更では、次のファイルは変更不要です。

- `boards/shields/CLine46/CLine46.dtsi`
- `boards/shields/CLine46/CLine46_L.overlay`
- `boards/shields/CLine46/CLine46_R.overlay`
- `boards/shields/CLine46/CLine46_L.conf`
- `boards/shields/CLine46/CLine46_R.conf`
- `config/west.yml`

これらは配線、左右分割、トラックボール、バッテリー、ZMK モジュールなどに関わります。キー入力の割り当てだけを変える場合は、`config/CLine46.keymap` に集中するのが安全です。
