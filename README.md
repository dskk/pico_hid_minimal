# pico_hid_minimal

Raspberry Pi Pico2 でミニマルな HID Device を作るデモ。  
ベアメタルで tinyusb の examples/device/hid_generic_inout 相当のものを実装した。

基本の実装 singlemode と、少し発展させた実装 multimode がある。それぞれのディレクトリに README.md があるので、詳しい説明はそちらを参照のこと。

Interrupt Out のデータをエコーバックする実装なので、そのあたりの処理は汎用性に欠ける点に注意。  
全体的な建て付けは変更しなくても良いが、Keyboard 等の一般的なデバイスを作る際には GPIO から HID Report を作成するなどの処理が必要になる。

Pico2 想定ではあるが、ビルド周りを弄れば初代 Pico でも動かせるコードになっているはず。
