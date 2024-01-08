---
title: ダンスVFXパーティクルシステム
layout: default
---

# 説明 (Description)

LegasyパイプラインでVFXGraphのようなパーティクル表現をするためのアセットです。

VRChatのワールド(SDK3)での動作を確認していますが、アバターでの動作は保証しません。

範囲内のオブジェクト(アバター)からパーティクルが発生します。炎のような表現も可能です。
# サンプル (Sample)

VRChatSampleWorld => https://vrchat.com/home/world/wrld_55b29286-7486-4c10-9a11-bb7ae922c33e

Github => https://github.com/HhotateA/DanceVFX_ParticleSystem

# 制作者 (Creator)

@HhotatA_xR

# 動作確認環境 (Environment)

・Unity2019.4.31f

・VRCSDK3-WORLD-2021.11.08.14.28

# 規約 (Terms)

・VRChatの仕様変更により本パッケージの機能が使えなくなった場合、製作者は責任を負いません。

・本アセットはUnity2019.4.31fのPCWindowsビルド環境で動作を確認しています。それ以外でのPlatformでの動作は保証しません。

・著作権は製作者に帰属します。

・本アセットを使って発生した問題に対しては製作者は一切の責任を負いません。

・Unityでビルド後であれば連絡なしで再配布可能とします。(VRChatのアバター、ワールドのアップロードはビルドと同じ扱いと考えます)

-The creator is not responsible if the functions of this package cannot be used due to a change in the specifications of VRChat.

・ This asset has been confirmed to work on PCWindows build environment of Unity2017.4.28. Operation on other platforms is not guaranteed.

・ Copyright belongs to the "HOTATE_NEKO".

-"HOTATE_NEKO" does not take any responsibility for any problems that occur using this asset.

-After building with Unity, it can be redistributed without contact. (I have confirmed that this is available for the VRChat world. It is unconfirmed whether this can be used for VRChat avatars.)

# 導入手順 (Installation procedure)

## 基本

1. Unityプロジェクトを作成する。(あらかじめVRChatSDKを導入しておく)

2. 本アセット(DanceVFX_ParticleSystem.unitypackage)をプロジェクトにインポートする。

3. "Assets/HhotateA_Assets/DanceVFX/Prefab/VFXParticleSystem"を自分のシーンに配置する

4. "VFXParticleSystem/System"のデプスカメラの範囲内のPlayerレイヤーのオブジェクトの周辺にパーティクルが発生します。

5. "VFXParticleSystem/Settings"内の各種Settingマテリアルの数値を変更することで、パーティクルの動きや見た目を変更できます。

## VideoPlayer/AudioLink対応版

1. Unityプロジェクトを作成する。(あらかじめVRChatSDK/iwaSync3/audio-linkを導入しておく)

2. 本アセット(DanceVFX_ParticleSystem.unitypackage)をプロジェクトにインポートする。

3. "Assets/HhotateA_Assets/DanceVFX/Sample"シーンを開き，VFXParticleSystemをコピーして任意のシーンに設置する．

4. "VFXParticleSystem/System"のデプスカメラの範囲内のPlayerレイヤーのオブジェクトの周辺にパーティクルが発生します。

5. "VFXParticleSystem/Settings"内の各種Settingマテリアルの数値を変更することで、パーティクルの動きや見た目を変更できます。

1. 各種オブジェクトの出し入れ
- Credit : クレジット表記のOn/Off (Local)
- Mirror : ミラーのOn/Off (Local)
- Video : 背景動画のOn/Off
- Debug : デバッグオブジェクト(メモリ)のOn/Off (Local)
- Floor : 床のOn/Off (Local)

2. エフェクトのプリセットを切り替えできる
- FireEffect : 炎っぽいエフェクト
- AttractorSphere : ステージ中央の球体にパーティクルが集まるエフェクト
- AvatarColor : アバターの色のパーティクルエフェクト
- RenderTrail : ゆっくり進むトレイルエフェクト
- RenderLine : 線型のパーティクルエフェクト
- RenderWireMesh : パーティクル同士を線でつないだエフェクト
- ParticleStrip : パーティクルの残像が残るエフェクト
- RenderStrip : テクスチャ型のパーティクルエフェクト
- SpriteTrail : 残像を残しながらふわふわ浮いていく，テクスチャ型パーティクルエフェクト
- CustomMode : 各種設定のできるモード
- OFF : パーティクル無効

3. パーティクル共通設定
- AvatarParticle : Enableにすると，範囲内のアバターからもパーティクルが出る
- VideoParticle : 数字を上げると再生中の動画からパーティクルが出る（数値が低いと，動画の明るい場所からのみパーティクルが出る）

4. CustomSetting
- LineRenderer : 線型パーティクルの大きさ（長さ）
- ParticleRenderer : 丸型パーティクルの大きさ
- ColorTemp : パーティクルの色（青<=白=>赤）
- EmissionRate : パーティクルの発生濃度
- Lifetime : パーティクルの寿命
- NoiseVelocity : パーティクルにランダムな動きを加える
- AttractorVelocity : 球体にパーティクルが集まる吸引力
- BarrierField : 球体の周りにパーティクルが近づけない領域を作る
- GravityForce : 重力の強さ
- ForcePower : パーティクルに一定の動きを加える
- FieldDrag : パーティクルの動きにかかる空気抵抗
- MaxSpeed : パーティクルの速度制限
- TrailMemory : パーティクルに残像を残す設定
- AlphaBrightness : パーティクルの明るさ
- SaturationCorrect : パーティクル色の彩度補正

# 設定 (Configuration)

Lifetime : パーティクルの寿命(minからmaxの間でランダムに決まります)
ParticleRate : フレーム当たりに生成されるパーティクルの量を変更できます
Motion : 物体の動きを検知する許容値(-0.01で常時発生)
Drag : パーティクルの速度減少(空気抵抗)です
MaxSpeed : パーティクルの最高速度です

StartSpeed : パーティクルの初速です

ParticleForce : パーティクルにかかる力です(風,重力等)
ForceOverLifetime : パーティクルの寿命ごとに力を加えられます

NoiseVelocity : パーティクルの動きにノイズを加えます(カールノイズ)
NoiseOverLifetime : パーティクルの寿命ごとにノイズの大きさを変更できます

ParticleColor : パーティクルの色です(aが低いとパーティクル発生源のオブジェクトの色がブレンドされます)
ColorOverLifetime : パーティクルの寿命ごとに色を変更できます

ParticleSize : パーティクルの大きさです
SizeOverLifetime : パーティクルの寿命ごとに大きさを変更できます

Attraction : パーティクルをControllerに引き寄せます
(AttractorMul,AttractorCenter) : パーティクルを引き寄せる中心をずらす設定です。オブジェクトの位置によって適切に設定してください。

# 任意アセット

・ iwaSync3 メディアプレイヤー
サンプルシーンでVideoParticleの実装に使用．
https://booth.pm/ja/items/2666275

・vrc-udon-audio-link
オーディオリンク対応版の動作に必要．
https://github.com/llealloo/vrc-udon-audio-link

・PostProcessing Version3.1.1

# 更新履歴 (Change log)

2020/03/03 v1.0
2021/11/29 v3.0 SDK3用に更新