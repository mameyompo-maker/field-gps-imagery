# field-gps-imagery — field_gps_map の地図データ(衛星写真・道と川・行政境界)

GPS 記録アプリ [field_gps_map](https://github.com/mameyompo-maker/field-gps-map) が、起動すると自動で全部取り込む
地図データの置き場。GitHub Pages(main / docs)で配る。v2.0.0(2026-10-24)から。

## 中身

- `docs/catalog.json` … 目録。アプリはここに載っている範囲を全部入れる(`tools/make_catalog.py` が作る)
- 範囲ごとのフォルダ(`docs/monapo/`、`docs/malema/`)
  - `index.json` … 範囲・ズーム(8〜14)・撮影日・束の一覧
  - `packs/pNN-<ハッシュ>.bin` … タイルを約 8 MB ずつまとめた「束」。アプリはこれを取り込む
    (Android の Chrome は Background Fetch で、アプリを閉じても続く)。
    形は `'FGMP'` + 見出しの長さ(4 バイト LE)+ 見出し JSON `{"tiles": [[z/x/y, 開始, 長さ], ...]}` + 中身
  - `{z}/{x}/{y}.webp` … 同じタイルを 1 枚ずつ(電波のあるときに、まだ入っていない所を直接読む用)
  - `osm.json` … 道・川・池・集落名(OpenStreetMap、© OpenStreetMap contributors、ODbL)
  - `admin.json` … 郡・行政ポスト・localidade の境界と名前(OCHA/INE COD-AB、HDX、v02 2025-01-01)
- `docs/s2/` … v1.4.0〜v1.7.0 の Malema〜Ribáue の画像(TCI、8 ビット)。Malema〜Ribáue の高画質版(`malema/`)ができるまでは
  目録がこれを指す。古い版のアプリもこれを読むので、しばらく残す。

## 衛星写真

- Copernicus Sentinel-2 L2A の 10 m バンド(赤・緑・青)を 16 ビットのまま、雲・影を除いて画素ごとに中央値で合成
  (2026-07-15〜10-22 の乾期)。Monapo の印刷用地図と同じ色の調整をかけて、ズーム 14(約 9 m/画素)の WebP に切った。
- 出典の表示:**Contains modified Copernicus Sentinel data 2026**(アプリの地図に描いている)。無料・商用利用可。
- Google・Mapbox・Esri の衛星写真は、規約でこのような Web アプリへの保存・配布が許されていないので使わない。
- 1画素 10 m なので、家 1 軒ずつは写らない。道・川・畑のかたまり・集落は見える。

## 作り直す・範囲を足す

`field_gps_map/tools/` のスクリプトで作り、ここに置いて push する。アプリ側の変更は要らない。

1. 16 ビットの雲なし合成(Monapo:`Community_nursery/04_monapo_gps/fetch_sentinel2_hq.py`、Malema〜Ribáue:`tools/s2_hq/`)
2. `python tools/hq_tiles.py <画像> <画像.json> ../field_gps_imagery/docs/<範囲> --id <範囲> --name "<名前>"`
3. `python tools/osm_region.py ../field_gps_imagery/docs/<範囲>/osm.json W S E N`
4. `python tools/admin_region.py ../field_gps_imagery/docs/<範囲>/admin.json <moz_admin4.geojson> W S E N`
5. 範囲を足すときは `tools/make_catalog.py` の `REGIONS` に1行足して、`python tools/make_catalog.py ../field_gps_imagery/docs`
