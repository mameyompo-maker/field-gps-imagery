# field-gps-imagery — field_gps_map の圏外用衛星写真(Sentinel-2)

GPS 記録アプリ [field_gps_map](https://github.com/mameyompo-maker/field-gps-map) が、
「地図を保存」したときに取り込む衛星写真のタイル置き場。GitHub Pages(main / docs)で配る。

- 中身:`docs/s2/{z}/{x}/{y}.webp`(256px、ズーム 8〜14)と `docs/s2/index.json`(範囲・撮影日・枚数)
- 範囲:Malema と Ribáue の中間を中心に、一辺 200 km(半径 100 km)
- 画像:Copernicus Sentinel-2 L2A の TCI(自然な色、1画素 10 m)。2026年8〜10月(乾期)の雲のない画像をつないだもの
- 出典の表示:**Contains modified Copernicus Sentinel data 2026**(アプリの地図に描いている)
- 作り方:`field_gps_map/tools/s2_tiles.py`(Earth Search の公開データから作る。登録不要)

## なぜ Google の衛星写真ではないか

Google・Mapbox・Esri の衛星写真は、規約でこのような Web アプリへの保存・配布が許されていない。
Sentinel-2 は EU の Copernicus 計画のデータで、無料・商用利用可(出典の表示が条件)。
その代わり粗い(1 ha の畑は 10×10 画素ほど)。道・川・畑のかたまり・集落は見えるが、細かい境界は見えない。

## 作り直す・範囲を広げる

`tools/s2_tiles.py` の `CENTER`・`HALF_KM`・`DATES` を変えて実行し、`docs/s2/` を差し替えて push する。
アプリ側の変更は要らない(index.json の範囲を読む)。
