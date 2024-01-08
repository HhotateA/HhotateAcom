---
title: 0から始めるQuestビルド
layout: default
---

#まえがき
　先日ついにスタンドアローン(PC接続なし)で6DoFのVR体験が可能なOculusQuestが発売されましたね。さっそくストアのゲームをプレイしましたが、スタンドアローンでこのクオリティ？と驚かされるコンテンツばかりでした。
　でも、やっぱり自分で作った空間をVRで体験したい！自分のためだけのVRコンテンツを作ってみたい！と思った人のためにUnityでQuest用のビルドをする方法をまとめます。今回はUnityHubのインストールからOculusIntegrationのサンプルシーンをQuest上で動かすことを目標に手順を解説していきます。(今回はたぶんスクリプトを1行も書かずにビルドまでするので気楽に読んでね！)
##OculusQuestの準備
###1.PCとの接続
　Questで開発を行うにはPCに接続するためのUSBTypeCケーブルが必要です。コツコツとUnityでアプリを作ってもUSBケーブルが無いと悲しいことになってしまうので、家に偶然USB-USBTypeC変換ケーブルなどがなかった人はあらかじめ準備しておきましょう！
###2.Oculus開発者登録
 Oculusの開発者登録をしていない場合は https://dashboard.oculus.com/ から開発者登録をしておきます。リンクを開いたら適当な名前で団体を創ればOKです。
###3.QuestをPCに接続する
　スマホのOculusアプリからQuestの開発者モードをONにします。
設定タブ>OculusQuest>その他の設定>開発者モード　の順に押して開発者モードがONになっているか確認してください。
　![40d019b065ea0d9c55ca55cb12e446e8.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/61467d78-f01f-582a-426f-65cfc2634f95.png)
　開発者モードをONにしたらQuestをPCに接続します。USBケーブルでPCとQuestをつなぐと、Quest内の画面でUSBデバッグの許可を聞かれるので許可しておきましょう(チェックボックスにチェックを入れておくと今後楽)。
###AndroidSDKのインストール
　さっきまで忘れてました。0から始めたPCにはAndroidSDKが入っていないので、下リンクからAndroidStudioをインストールしておきましょう。
https://developer.android.com/studio/
##Unityの準備
###1.UnityHubのインストール
　UnityHubは複数のバージョンのUnityを簡単に管理するためのツールです。下のリンクからダウンロードしてインストールしてください。
https://unity3d.com/jp/get-unity/download
![dc7a0a497abdeab2a1cc7f2da5b214c9.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/33da6203-ff17-f524-993f-7fe2dce83554.png)
　UnityHubのインストールが終わり起動すると以下のような画面が出てきます。
　初期状態のままではUnity自体がインストールされておらず、なにもつくれないのでUnityをインストールしていきます。まず<font color="blue">①のインストールタブ</font>次に<font color="red">②のインストールボタンを押します</font>。
![49e257fb1425d593f531525bbae4c098.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/29058c30-79f4-d54b-6857-ed742968038c.png)
　するとUnityのバージョンを選択できる画面になるのでとりあえず最新の安定バージョン(バージョン名の最後にfがついているもの)を選びましょう。
　次の画面では**Android Build Supportのチェックを忘れずに入れて**次へを押します。
![d44c6a319af11e81f079987d8e583b2c.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/6be64ad7-41d0-41af-3c3e-52879f7fc038.png)
　その後Unityのインストールが終わるまでしばらく待ちます。(ｹｯｺｳﾅｶﾞｲﾖ)
###2.プロジェクトの作成
　インストールが終わったら、<font color="blue">①のプロジェクトタブ</font>次に<font color="red">②の新規作成</font>を押します。
![スクリーンショット (59).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/8c2d6db8-ccf7-8875-e4b1-ab3f8b798a33.png)
##Unityの設定項目をQuest用にする
###プラットホームの変更
　UnityはデフォルトでPC向けのモードになっているので、Quest向け(Android環境)に切り替えましょう。
File>BuildSetting でBuildSettingウィンドウを出して、<font color="blue">①Androidのタブ</font>を選択後<font color="red">②SwitchPlatformのボタン</font>を押します。
![スクリーンショット (60).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/73e6dc1f-0b11-1d18-20c2-a2d1b438a80b.png)
###ビルド設定のなんやかんや
　BuildSettingウィンドウの左下のPlayerSettingボタンを押してPlayerSettingウィンドウを出します(Playerタブになっているか確認)。
<font color="blue">①CompanyName</font>が他アプリと同じだと上書きされてしまうので、被らないように変更しておきましょう。次にOtherSettingを押して、上の方の<font color="red">AutoGraphicsAPI</font>にチェックを入れ(Valkanを消せばいいけどAutoのが楽)、その下の<font color="orange">③MinimumAPILevel</font>をAndroid7.1にします。
![5b556c150db2fd2aebcced17a9aaf165.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/71a0ebdd-76ff-06e2-85c2-2597481c4739.png)
　PlayerSettingの一番下にXR Settingsの項目を開き、VirtualRealitySupportedのチェックを入れて、VirtualRealitySDKsにOculusを追加しておきます。
![alt](https://i.gyazo.com/049a8b1cd5fa2003ade077130071d4a1.png)
　これで基本的な設定は終了です。
##OculusIntegrationのインポートといろいろ
###OculusIntegrationのインポート
 OculusIntegrationはOculusがUnity向けに提供しているVR用のスクリプト集です。サンプルも豊富でOculus向けのアプリを作るならほぼ必須のツールだと思うので早速インポートしましょう。
Unityで<font color="blue">①AssetStore</font>を開きOculus Integrationを検索します。たぶん1つしかないので、それをDownloadごImportします。<font color="orange">③</font>のような画面が出てきたらImportのボタンを押します。
![alt](https://i.gyazo.com/d9c798dc1de28822484358d48abee0da.png)
###サンプルシーンを開く
 Unity画面下部のAssetsから先ほどインポートしたファイルを開きます。Oculus>SampleFramework>Usageの順にフォルダを開いてゆき、<font color="blue">①AvatarGrabのシーン</font>ダブルクリックでを開きます。それっぽいオブジェクトが上の画面に出てきたらあってます(AvatarGrabの他にも様々なサンプルシーンがあるので、好きなもので試してみてもいいかもですね)。
次にカメラのスクリプト等をQuest用にするのでUnity画面左側のHierarchyから<font color="red">②LocalAvatarWithGrab>OVRCameraRig</font>を左クリックで選択状態にします。すると画面右側の<font color="orange">③InspectorにTargetDevices</font>という項目が出てくるのでここをQuestにします。
![alt](https://i.gyazo.com/fb73185fe7a46fa58fd8dbb8c4b189fe.png)
##いよいよビルド！
　File>BuildSetting でBuildSettingウィンドウを出して、AddOpenScenesを押して現在開いているサンプルシーンを追加しておきます。
　QuestがPCに接続されていることを確認後、BuildSettingウィンドウ右下部の"BuildAndRun"ボタンを押してapkファイルを適当なフォルダに保存します。初回のビルドは結構長いので、祈ったりして過ごしましょう。
　ビルドが最後まで終わるとQuestで勝手にアプリが起動します。下のようにコントローラーでブロックを積み上げられるようになっていれば完璧です！
![alt](https://i.gyazo.com/bccb234b49a68b2795e97bd92c735d22.jpg)
　アプリを閉じてUSB接続を解除しても、ライブラリ>提供不明のアプリの中にビルドしたアプリが残るので、外出先でも簡単に自作のアプリを体験してもらうことができます。
![alt](https://i.gyazo.com/ad944695e3820d1d42d96550debdf886.jpg)
##Questマニュフェストの追加(2019/08/16追記)
　なんかマニュフェスト書かなきゃコントローラーが片っぽしか認識しないらしい。
Assets>Plugin>Android>AndroidManifest.xml
に
`<uses-feature android:name="android.hardware.vr.headtracking" android:required="true" android:version="1"/>`
を追記する。

##おしまい
　今回はほ"ぼほぼ初めてUnityを触る人がQuest用アプリビルドまでたどり着ける"というコンセプトの記事だったので、かなり画像多めで内容が薄くなってしまいました。これを参考にQuestでのアプリ開発に興味を持った人が一人でもいてくれたらうれしいです。
　次があったら、もうちょい踏み込んだ解説をします。おやすみなさい。。。zzz
