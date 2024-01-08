---
title: 自分の部屋をデジタル空間に持っていった話
layout: default
---

自分の部屋をVRChatにアップロードしたので、そのあたりの話を。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ここがたった一つの現実だと思った？ <a href="https://t.co/7ignemc1YF">pic.twitter.com/7ignemc1YF</a></p>&mdash; ほたてねこまじん🐾にゃんにゃん (@HhotateA_xR) <a href="https://twitter.com/HhotateA_xR/status/1179635312034643975?ref_src=twsrc%5Etfw">October 3, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">おまけ<a href="https://twitter.com/hashtag/VRChat?src=hash&amp;ref_src=twsrc%5Etfw">#VRChat</a> <a href="https://twitter.com/hashtag/VR?src=hash&amp;ref_src=twsrc%5Etfw">#VR</a> <a href="https://twitter.com/hashtag/HMD?src=hash&amp;ref_src=twsrc%5Etfw">#HMD</a> <a href="https://twitter.com/hashtag/RealityCapture?src=hash&amp;ref_src=twsrc%5Etfw">#RealityCapture</a> <a href="https://twitter.com/hashtag/%E3%83%95%E3%82%A9%E3%83%88%E3%82%B0%E3%83%A9%E3%83%A1%E3%83%88%E3%83%AA%E3%83%BC?src=hash&amp;ref_src=twsrc%5Etfw">#フォトグラメトリー</a><a href="https://twitter.com/hashtag/Photogrammetry?src=hash&amp;ref_src=twsrc%5Etfw">#Photogrammetry</a> <a href="https://t.co/gmYmEme8kB">pic.twitter.com/gmYmEme8kB</a></p>&mdash; ほたてねこまじん🐾にゃんにゃん (@HhotateA_xR) <a href="https://twitter.com/HhotateA_xR/status/1181491331270467584?ref_src=twsrc%5Etfw">October 8, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　この部屋は、"フォトグラメトリー"という2Dの写真から3Dのオブジェクトを復元する技術を使ってVRChatにアップロードしました。　フォトグラメトリーは写真の撮り方などに多少のコツなどありますが、無料のソフトでもそれなりに部屋を再現することができます。
こちらから見れます　→ https://www.vrchat.com/home/launch?worldId=wrld_5cf851a5-eb94-4e1f-82be-c4283ba57747&instanceId=45250
　今回はVRChater向けに自分の部屋をVRChatにアップロードするという目標で記事を書いていきます。

# 部屋を片付ける
　一番重労働！
　床に物が落ちていたりすると写真を撮っている途中でモノが動いてしまい、フォトグラメトリーがうまくいかないので物を片付けましょう。部屋の中心に椅子があると、邪魔になるのであらかじめ部屋の外に出しておいたほうがいいかもしれません。
あと、意味があるかは微妙ですが、まっしろな壁はフォトグラメトリーで再構築しづらいので、ポスターなどを貼っておくとよいです。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">特徴点 <a href="https://t.co/u9N8hOh7hd">pic.twitter.com/u9N8hOh7hd</a></p>&mdash; ほたてねこまじん🐾にゃんにゃん (@HhotateA_xR) <a href="https://twitter.com/HhotateA_xR/status/1177533782347276289?ref_src=twsrc%5Etfw">September 27, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">思ったより効果あった(*‘ω‘ *) <a href="https://t.co/P4aElpBfXb">pic.twitter.com/P4aElpBfXb</a></p>&mdash; ほたてねこまじん🐾にゃんにゃん (@HhotateA_xR) <a href="https://twitter.com/HhotateA_xR/status/1178531045903237120?ref_src=twsrc%5Etfw">September 30, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

　窓や鏡,PCディププレイなどの反射物はエラーの原因になるので、カーテンを閉める,ディスプレイに何か表示するなどの対策を取ります。

# 写真を撮る
　スマホや1眼カメラなどで部屋の写真を撮ります。複数枚の写真を撮るうえで、写真ごとに明るさが変わってしまうと困るので絞りとシャッター速度を固定します(Fは高め、シャッター速度は早めがおすすめです)。手振れはエラーの原因になるので、しっかり落ち着いて撮りましょう。

　またフォトグラメトリーの処理には写真どうしがある程度被っている必要があるので、それを意識しながら撮っていきます(RealityCaptureの初期設定では65%)。また物の形が写真から読み取れるように、対象物に対して回り込みながら撮影します。
　![8e36f4ec1cf6a51c20e66efcb0292ec5.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/876a6350-1b0d-8813-30ed-1eb33f0a4d1f.gif)　➡　![d03a2d7c1ea85c8aed7404b6328816b3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/933d5721-2c27-6caa-e305-6c55ae951b8b.png)

　部屋の撮影をするときはできるだけ広い範囲を一度に撮れるように部屋の外周を回りながら、部屋の内側にカメラを向けて撮影します。
![heya.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/428a3a06-7f85-c37a-d977-f2b71ca192c2.png)　![feef89ef45786f216412260381fac6a8.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/a1d4e46f-52b3-86de-2fcc-f0d3350b9066.gif)
　また同じ場所でも、"低い場所から上に向けて","普通の高さから前に向けて","高い場所から下に向けて"と何回か撮影するのがコツです。
![kabe.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/e943e250-f272-6e3f-5b3f-0450608f4da9.png)　![6dbf2e41f392dcd87af7c9360be04b9e.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/df7b26bb-225e-19ed-800f-8c6f42aeb56e.gif)

　部屋全体を撮影した後、特に気になる小物を撮影しておくとうまくいきやすいです。
　今回は7畳ほどの部屋で合計700枚の写真を撮りました(ちょっと多すぎかも)。

# フォトグラメトリーのソフトに画像を読み込む
　フォトグラメトリーができるソフトには無料のものと有料のものがあります。無料でも十分なモデルは作成できますが、こだわるならRealityCaptureなどの有料ソフトを使うこともお勧めします！
　今回はMeshRoomとReaityCaputureのそれぞれでフォトグラメトリーをしてみます。

### MeshRoom(無料)
　①MeshRoomを公式サイトからダウンロードします。
https://alicevision.org/#meshroom
![8a5c137782dd402602318456519c3478.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/fa67a43e-f773-84bf-8946-7bea32ae2236.png)
　②MeshRoomを起動します。（ここのチェック入れないとダメかも）
![6fc8e39112ca5cac31bda8b860382177.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/4d6a5879-3686-b3b9-824c-0ebe2f773a12.png)
　③先ほどの画像をドラッグ＆ドロップで読み込みます。
![05d3ed53b3115d0ccf26cd045e6a4c71_1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/f9fb1d83-8f84-825c-c484-8900ef2bf4b3.png)
　④プロジェクトを保存した後、上の"Start"ボタンを押して処理を開始する。(部屋とかだと1日以上かかるかも)
![9fe627716e785a4927028cbf29cfc3ea_1.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/7240238d-5d22-06a4-75d9-ec0e650243f7.jpeg)
　⑤RoadModelが表示されたら終了。
　プロジェクトを保存したフォルダの MeshRoomCash\Texturing{数字とアルファベットのフォルダ}\texturedMesh.obj,texturedMesh.mtl,texture_1001.png
あたりをUnityにインポートすれば読める
![d4463f500618ac362936f3b581ff6789.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/59d4fa72-8bed-2ef9-d77d-b10184ee0d3b.jpeg)

#### よく使う設定項目
CameraInf>DefaultFieldOfView 撮影に使ったカメラの視野角。
DepthMap>DownScale デプスマップのダウンスケール。下げればクオリティが低くなる代わりに処理が速くなる。
Meshing>MaxPoints 出力メッシュの最大ポリゴン数。ポリゴン数が多すぎるとUnityがまともに動かないので、10万程度にする。
MeshFiltering>SmoothingInterations メッシュのスムース。ノイズが減るがメッシュが消えることがある。
Textureing>FillHoles テクスチャの隙間の色が補完される。
![5730229be9b4f3f5993e51b4eff79e28.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/eb4fd137-887a-92b0-e4e0-1655035bd147.png)

### RealityCaputure(有料)
　①公式サイトもしくはSteamからRealityCaputureをサブスクライブ、ダウンロードする。
　(何度も出力を試せるので、Steamでのサブスクライブが個人的におすすめです)
https://store.steampowered.com/app/489180/RealityCapture/?l=japanese
　②先ほどの画像をドラッグアンドドロップで読み込む。
![387fa5240d267293535648fd2a3eac58.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/078b2085-9bff-e680-0466-ab91240311d7.png)
　③WorkFlowタブのProcess>Startを押して処理を開始します。
![ac9e686883df80037debec0f8b971d5d.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/72450e2e-8270-3455-3be1-4c1f8b7593e2.png)
　④ReconstructionタブのTools>SimplifyToolを起動し、右下に出てきた設定ウィンドウのTargetVertexCountを10万程度に設定しSimplifyを押しポリゴン数を減らす。
　⑤ReconstructionタブのModelColor&TextureのTextureを押してテクスチャを貼る。
![cc3a64988fcdc06af1c7de14d044befe.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/aade8163-4c18-327c-9907-056d91eb8be5.jpeg)
　⑥ReconstructionタブのExport>Modelからfbxを選択しFBXとして出力する。
![dfdbe6ce62b1198a8728fd2ddfe25ce7.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/6dfb0031-31be-a6cd-bdba-53e08c476642.jpeg)


#### よく使う設定項目
Color&TestureSettings>DefaultUnwrapPaqramenters>MaximalTextureResolution 出力テクスチャの解像度
Color&TestureSettings>DefaultUnwrapPaqramenters>MaximalTextureCount 出力テクスチャの枚数
AligmentSetting>MaxFeatures>MaxFeaturePerMpx メガピクセルごとの特徴点の最大数(160kくらいがいい)
AligmentSetting>MaxFeatures>MaxFeaturePerImage 画像ごとの特徴点の最大数？
AligmentSetting>MaxFeatures>ImageOverlap 画像同士の重なり(デフォルト65%)
MaxFeatureReprojectionError 許容するエラー？(大きめにしたらシンプルな模様のベッドがループして伸びた)
![e36c18da6f2218b711fb408e9a759929.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/d393ff8a-d2b8-8f7d-6b1a-b0ecea571490.png)


# モデルを修正する
　BlenderやMeshLabでモデルを読み込み、穴埋めやアーティファクト(余計なポリゴン)を消す。
![5962136b9cc7004ecd0fa5704320279d.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/90c36fe8-93f3-19b7-88aa-12a6ff555d20.png)

# Unityに読み込む
　①fbxファイルとテクスチャをUnityに読み込む。シーンにドラッグ&ドロップする。
![89a8b2e55346630bdc14c63a2fe68e66.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/6055d717-6025-755b-e7bc-9aa1c8971d42.png)
　②マテリアルのシェーダーをUnlit/Textureにする。
![a45b337ee0764759dea9ed3eaea232b2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/f5129bf8-65a5-c7e3-34f5-54d5875d3f69.png)
　③テクスチャがぼやけている場合は、テクスチャ解像度が適切に設定されていない可能性があるので、importSettingから設定する。
![e9eb9cbfe8a4e3e86400f4952e4be056.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/616baf7b-5242-c456-b3cc-8da786e3e051.png)
　④Cubeを組み合わせていい感じにコライダーを作る。
![4bdd051af6bbdae57a9b6d95ce8cc945.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/add25182-df61-706d-0749-94c27b304ff4.png)
　⑤VRChat等にアップロードする。


# おわり
　VRChatでフォトグラメトリーの部屋を集めた企画をしているので、興味あればどうぞー。
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">フォトグラメトリーで自分の部屋を再現するの興味持たれた方はこちらどうぞー<br>VRChatにフォトグラメトリーアパートを作ろうプロジェクトです！<a href="https://t.co/8DtStNhZ7u">https://t.co/8DtStNhZ7u</a></p>&mdash; ほたてねこまじん🐾にゃんにゃん (@HhotateA_xR) <a href="https://twitter.com/HhotateA_xR/status/1179293546492223488?ref_src=twsrc%5Etfw">October 2, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


