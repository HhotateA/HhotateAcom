---
title: CameraDepthTextureからワイヤーフレームを計算する
layout: default
---

# あいさつ
こんにちは！STYLY XRエンジニアのほたてねこまじんです ฅ(＾・ω・＾ฅ)
今回はアドベントカレンダーということで、STYLYでも使えるUnityシェーダーでできるエフェクト一工夫を紹介していきたいと思います。

![15e05bbc-6ed6-4b9c-9556-89f3aa87d974_base_resized.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/df464b4f-fb11-80f7-b633-b9c1d1324723.jpeg)

通常シェーディングは物体をリアル（物理的に正しく）描写するために存在します。
ですが逆に、ワイアフレーム表示にすることでCGらしさを強調したり、テクスチャを省略して非現実感を出したり、リアルではないシェーディングにも一定の価値や魅力はあるのは事実でしょう。
今回はそんなリアルではないシェーディングをフィルターとして実装するお話です。

# フィルターで実装する
実際のところワイアフレーム表示する機能自体はUnityでもデフォルトで存在しており、アタッチしたオブジェクトをワイアフレーム化するシェーダーも数多とあるでしょう。しかし、今回は眼鏡のレンズのように透かして見ることであらゆる物をワイアフレーム表示するシェーダーを作っていきます。
ですので、[STYLY](https://gallery.styly.cc/studio)のようなUGCプラットフォームでは、個別にアップロードする手間が省けたり、他人が作ったオブジェクトにも適応できるというメリットがあります。
逆に、"オブジェクトごとに効果を切り替えたい""このオブジェクトだけはワイアフレーム表示したくない"という場合は他の方法を考える必要があります。

# 原理
まず、どのようにワイアフレームを計算するかの原理を考えていきます。
[前回の記事](https://qiita.com/HhotateA/items/421fd49fc3c9a85dde8c)でも紹介したとおり、Unityではカメラからオブジェクトまでの距離（奥行）が取得できます。今回もその値を元にワイアフレームを計算していくことになります。

https://qiita.com/HhotateA/items/421fd49fc3c9a85dde8c

## 1.オブジェクトのワールド座標の取得
_CameraDepthTextureから逆算し、各ピクセルのワールド座標を求めます。
[前回の記事](https://qiita.com/HhotateA/items/421fd49fc3c9a85dde8c)でも紹介しているので省略っ！

```c#:filter_wireframe.hlsl
v2f vert (appdata v)
{
    v2f o;
    o.vertex = UnityObjectToClipPos(v.vertex);
    o.wpos = mul(unity_ObjectToWorld,v.vertex); 
    o.projPos = ComputeScreenPos(o.vertex);
    return o;
}

float3 getWorldPos(float4 projPos,float3 wpos)
{
    float depth = Linear01Depth(tex2Dproj(_CameraDepthTexture, projPos).r);
    float3 cameraVec = normalize(wpos-_WorldSpaceCameraPos);
    // オブジェクトのワールド座標 = カメラのワールド座標 + カメラからフィルターまでのベクトル × デプス距離
    float3 worldPos = _WorldSpaceCameraPos + cameraVec * depth;
    return worldPos;
}

half4 frag (v2f i) : SV_Target
{
    float3 worldPos1 = getWorldPos(i.projPos,i.wpos);
    return float4(frac(worldPos1*30),1); // 見やすいようにちょっと値を編集
}
```
![6ac17447d3ccfa086ebe8d1553f36d39.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/87e99aa7-8e50-6b29-643f-21a55a5adb26.png)

## 2.ワールド座標からノーマルマップへ
各ピクセルのワールド座標がわかったことで、各ピクセルにおけるオブジェクトのポリゴン面の傾き（ノーマルマップ）が取得できます。
ノーマルマップとはオブジェクトの凹凸をRGBで表したもので、座標とは微分の関係にあります。
微分と聞いても怖がる必要はありません。数列における微分とはただの引き算です！（言葉の定義が若干違いますが怒らないで）
1.で取得したを2次の数列と考えてA(x,y)と置きます。A(x,y)からはワールド座標が取れるので(r,g,b)の3つの値が取れます。(x,y)におけるポリゴン面の傾きは、微分(=隣のピクセルとの引き算)で求まりますので、
ノーマルマップテクスチャB(x,y)はワールド座標テクスチャA(x,y)を用いて`B(x,y) = A(x,y) - A(x-1,y-1)`のように求まります。

ここで、シェーダーにおいてはGPUは隣接4ピクセルごとに計算を行っており、`dFdx``dFdy`という便利関数を使用することでx,yそれぞれの偏微分を求めることが出来ます。
また、`fwidth(x)`という関数が`abs(dFdx(x)) + abs(dFdy(x))`と同じ結果を返すので、今回は隣接ピクセルまでのuv差を取得するために、この関数を取得します。（直接NormalMapをfwidthで取得しようとすると、隣接ピクセルごとに同じ結果になる都合上解像度が1/4になってしまう他、次手順で不具合が起こります。）
```c#:filter_wireframe.hlsl
half4 frag (v2f i) : SV_Target
{
    float4 projPosdd = fwidth(i.projPos); // 隣接ピクセルとのUV差
    float3 wposdd = fwidth(i.wpos);
    float3 worldPos1 = getWorldPos(i.projPos,i.wpos);
    float3 worldPos2 = getWorldPos(i.projPos+projPosdd,i.wpos+wposdd);
    float3 worldNormal = normalize(worldPos1-worldPos2); // 最後にノーマライズすることを忘れずに
    return half4(worldNormal,1.);
}
```
![4e7237cc2a5db9399ab0a1a2a65bb7d0.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/dfe24a07-8244-6d2e-090e-5a9851ee02b2.png)

## 3.ノーマルマップからワイアフレームへ
ここまででできた画像を見るとすでに、ポリゴンの傾きの際が線として見えています。スカートのレース部などのカクカクした部分がわかりやすいですね
この色の変わり目を線として表示できれば、ワイアフレームを表示できそうです。

では、色の変わり目はグラフでいうとピークに当たる部分です。微分することで、色の変化のない部分は0に、色の変化のある部分は+か-にグラフが行くのが想像できるかと思います。
つまり、前項目と同じ操作をすることでテクスチャとしてワイアフレームを取得できるようになります。
今回は前手順で使わなかった`fwidth(x)`を使用してみます。この結果のベクトル長が0でなければそのピクセルは、ポリゴンの傾きの変化点でありワイアフレームの描写をすべきということです。
```c#:filter_wireframe.hlsl
half4 frag (v2f i) : SV_Target
{
    float4 projPosdd = fwidth(i.projPos); // 隣接ピクセルとのUV差
    float3 wposdd = fwidth(i.wpos);
    float3 worldPos1 = getWorldPos(i.projPos,i.wpos);
    float3 worldPos2 = getWorldPos(i.projPos+projPosdd,i.wpos+wposdd);
    float3 worldNormal = normalize(worldPos1-worldPos2); // 最後にノーマライズすることを忘れずに
    float wireFrame = step(0.01,length(fwidth(worldNormal))); // 隣接ピクセルで微分し、ポリゴンの傾きの変化点を検知
    return lerp(_basecolor,_linecolor,wireFrame);
}
```
![90c08fd9caf6aaf3b55b8e754875d906.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/34c13289-3c83-b349-f1b8-af0bc4fc4199.png)

# 完成品
できたーฅ(＾・ω・＾ฅ)
```c#:filter_wireframe.hlsl
Shader "Qiita/Wireframe"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _basecolor ("BaseColor",color) = (0.0,0.0,0.0,0.0)
        _linecolor ("LineCOlor",color) = (1.0,0.0,0.0,1.0)
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float3 wpos : WORLD_POSITION;
                float4 projPos : PRO;
            };

            sampler2D _MainTex; float4 _MainTex_ST;
            float4 _basecolor;
            float4 _linecolor;

            sampler2D _CameraDepthTexture;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.wpos = mul(unity_ObjectToWorld,v.vertex);
                o.projPos = ComputeScreenPos(o.vertex);
                return o;
            }

            float3 getWorldPos(float4 projPos,float3 wpos)
            {
                float depth = Linear01Depth(tex2Dproj(_CameraDepthTexture, projPos).r);
                float3 cameraVec = normalize(wpos-_WorldSpaceCameraPos);
                float3 worldPos = _WorldSpaceCameraPos + cameraVec * depth;
                return worldPos;
            }

            half4 frag (v2f i) : SV_Target
            {
                float4 projPosdd = fwidth(i.projPos);
                float3 wposdd = fwidth(i.wpos);
                float3 worldPos1 = getWorldPos(i.projPos,i.wpos);
                float3 worldPos2 = getWorldPos(i.projPos+projPosdd,i.wpos+wposdd);
                float3 worldNormal = normalize(worldPos1-worldPos2);
                float wireFrame = step(0.01,length(fwidth(worldNormal)));
                return lerp(_basecolor,_linecolor,wireFrame);
            }
            ENDCG
        }
    }
}
```

# おしまい
今回は、Unityでできるシェーダーエフェクトを作成してきました。
制限の多いUGCプラットフォームの中でも、シェーダーは比較的自由にエフェクトを組める要素です。みなさんも、色々なシェーダー作成を試してみてください。

Psychic VR Labでは新メンバーを募集しています！
どうぞお気軽にご応募ください！
https://psychic-vr-lab.com/recruit/
