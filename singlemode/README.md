# pico_hid_minimal singlemode

動作モード (一連のディスクリプタの設定の組のこと) を一種類のみ有する基本のコード。  
usb_descs.h にディスクリプタを const で記載して、main.c で使う方式。

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
