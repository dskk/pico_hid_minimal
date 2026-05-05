# pico_hid_minimal multimode

singlemode のコードをベースに「複数の動作モードから、起動時に一つ選ぶ処理」を追加したサンプル。  
例えば「Gamepad モードと Keyboard モードがあるデバイス」などを作る雛形として活用する想定。  
最低限の実装よりはいくらか機能面も拡充されていて、ディスクリプタの記載ミスを発見するための関数 setup_usb_descs なども追加になっている。  
追加機能のための実装がどういうものであるかは、singlemode のコードとの diff を取ることで確認できる。

# build

.bashrc で export するなどの方法で、環境変数 `PICO_SDK_PATH` は設定済みとする。

```bash
mkdir build
cd build
cmake -DPICO_BOARD=pico2 ..
make
```

cmake のオプションはボード次第。 `cmake -DPICO_BOARD=pimoroni_pico_plus2_rp2350 ..` など。

# 動作確認

基本動作は singlemode のサンプルと同じだが、動作モードが二つある。  
機能に差はないが、Product を表す String Descriptor 末尾が "MODE0" と "MODE1" で異なるので、どちらで認識されているかが確認できるようになっている。  
動作モードはフラッシュの後ろの方の領域を読み出して決定していて、工場出荷時は MODE0。  
GPIO0 を low にしながら起動すると MODE0 の設定が、GPIO1 を low にしながら起動すると MODE1 の設定が、フラッシュに書き込まれたあと動作開始する。  
特に GPIO を触らずに起動するとフラッシュへの書き込みは起こらないので、設定は永続的である。

(以下、singlemode の README.md からそのまま転載)

build ディレクトリに生成された uf2 ファイルを書き込んで再接続すると HID Device として認識される。

Ubuntu 24.04 の場合、事前に `sudo apt install libhidapi-hidraw0` と `pip install hid` をすれば、以下のテストコードが実行可能になるはず。  
Windows の場合、apt install しなくて良いけど別の pip install が必要だった。  
WSL は仮想環境越しの USB の扱いが面倒なので非推奨。

tinyusb の hid_generic_inout 相当の動きをするので、テストコードは同じものが使える。  
https://github.com/hathach/tinyusb/blob/3170fa0bf2667c4cc8e5a22944c15686c681654e/examples/device/hid_generic_inout/hid_test.py

<pre>
$ sudo python test.py
VID list: cafe, 239a, 2e8a, 303a
{'path': b'/dev/hidraw0', 'vendor_id': 51966, 'product_id': 16404, 'serial_number': 'Build May  5 2026 18:31:51', 'release_number': 256, 'manufacturer_string': 'Pico', 'product_string': 'Minimal HID', 'usage_page': 65280, 'usage': 1, 'interface_number': 0, 'bus_type': <BusType.USB: 1>}
Send text to HID Device : hellodskk
Received from HID Device: b'hellodskk'
</pre>
