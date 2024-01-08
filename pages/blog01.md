---
title: モデルのアウトラインのみを描写するシェーダー
layout: default
---

![アウトラインのみを描画するシェーダー](https://qiita-image-store.s3.amazonaws.com/0/392903/fdc4eaad-7237-f70e-ebaf-c444c05759c8.png)
#はじめに
　トゥーンシェーディングではモデルのアウトラインを描画することが、大きな特徴です。
　一般的なトゥーンシェーダーは、「内側に面を向けて少し膨らませたオブジェクト」の後で「元のオブジェクト」を描写し、重なっていない部分をアウトラインとしています。このようなシェーダーでメインカラーを透明にしても、アウトラインごと透明になってしまうか、「内側に面を向けて少し膨らませたオブジェクト」が残ってしまい、うまく透過することができません。
　今回はアウトラインのみを描画するシェーダーを、ステンシルバッファーを使って実装します。
#実装
　一般的なトゥーンシェーダーではアウトライン部分は先述の2ステップ

>ステップ1.内側に面を向けて少し膨らませたオブジェクトを描写する。
ステップ2.元のオブジェクトを描写する。重ならず残った部分がアウトラインになる。

ですが今回は下の3ステップで実装します、

>ステップ1.内側に面を向けて少し膨らませたオブジェクトでステンシルを書き込む
ステップ2.元のオブジェクトでステンシルをリセットする
ステップ3.ステンシルが書き込まれている部分に色を塗る


###ステップ1.内側に面を向けて少し膨らませたオブジェクトでステンシルを書き込む
 ステンシルをもとのステンシル値にかかわらず42で上書きします。

```c#:outline.hlsl
            Stencil{
                Ref 42
                Comp always
                Pass replace
            }
```

　頂点シェーダーではこのオブジェクトを法線方向に少し膨らませます。

```c#:outline.hlsl
            v2f vert (appdata v){
                v2f o = (v2f)0;
                o.pos = UnityObjectToClipPos(v.vertex + v.normal * 0.05);
                return o;
            }
```

 フラグメントシェーダーは適当でいいです。(ColorMask 0でいい)


###ステップ2.元のオブジェクトでステンシルをリセットする
 ステンシルをもとのステンシル値にかかわらず0でリセットします。

```c#:outline.hlsl
            Stencil{
                Ref 0
                Comp always
                Pass replace
            }
```

　頂点シェーダーは特にいじりません。

```c#:outline.hlsl
            v2f vert (appdata v){
                v2f o = (v2f)0;
                o.pos = UnityObjectToClipPos(v.vertex);
                return o;
            }
```

こちらもフラグメントシェーダーは適当でいいです。


###ステップ3.ステンシルが書き込まれている部分に色を塗る
 ステップ1でステンシルが書き込まれてステップ2.でリセットされていない、ステンシル値が42の部分のみに描写するようにします。

```c#:outline.hlsl
            Stencil{
                Ref 42
                Comp eqal
                Pass keep
            }
```

　頂点シェーダーは今までのオブジェクトすべてを覆う必要があるので、このオブジェクトを膨らませます。

```c#:outline.hlsl
            v2f vert (appdata v){
                v2f o = (v2f)0;
                o.pos = UnityObjectToClipPos(v.vertex + v.normal * 0.05);
                return o;
            }
```

 フラグメントシェーダーではアウトラインの色をそのまま出力します。

```c#:outline.hlsl
         fixed4 frag (v2f i) : SV_Target{
                return _color;
            }
        }
```

#完成品
　下のようなコードになれば完成です。

```c#:outline.hlsl
Shader "Custom/outline" {
    Properties{
        _OutlineWidth ("Outline Width", float) = 0.1
		_color ("color",Color) = (1.0,0.0,0.0,1.0)
    }
    SubShader {
        Tags { "RenderType"="Transparent" "Queue"="Transparent" }
        ZWrite On

		Pass {
            Stencil {
                Ref 42
                Comp always
                Pass replace //ステンシル42を書き込む
            }

            Cull Front //面を内側に向ける
			ColorMask 0

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            
            #include "UnityCG.cginc"

            struct appdata {
                half4 vertex : POSITION;
                half3 normal : NORMAL;
            };

            struct v2f {
                half4 pos : SV_POSITION;
            };

            half _OutlineWidth;
            
            v2f vert (appdata v) {
                v2f o = (v2f)0;
                o.pos = UnityObjectToClipPos(v.vertex + v.normal * _OutlineWidth);//オブジェクトを法線方向に膨らませる
                return o;
            }
            
            fixed4 frag (v2f i) : SV_Target {
                return 0;
            }
            ENDCG
        }

        Pass {
            Stencil {
                Ref 0
                Comp always
                Pass replace //ステンシルを0でリセット
            }

            Cull back
			ColorMask 0

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
                
            #include "UnityCG.cginc"

            struct appdata {
                half4 vertex : POSITION;
            };

            struct v2f{
                half4 pos : SV_POSITION;
            };
            
            v2f vert (appdata v) {
                v2f o = (v2f)0;

                o.pos = UnityObjectToClipPos(v.vertex);

                return o;
            }
            
            fixed4 frag (v2f i) : SV_Target {
                return 0;
            }
            ENDCG
        }

        Pass {
            Stencil {
                Ref 42 
                Comp equal
                Pass keep //ステンシルが42なら描写
            }

            Cull back

           CGPROGRAM
           #pragma vertex vert
           #pragma fragment frag
            
           #include "UnityCG.cginc"

            struct appdata {
                half4 vertex : POSITION;
                half3 normal : NORMAL;
            };

            struct v2f {
                half4 pos : SV_POSITION;
            };

            half _OutlineWidth;
			fixed4 _color;
            
            v2f vert (appdata v) {
                v2f o = (v2f)0;

                o.pos = UnityObjectToClipPos(v.vertex + v.normal * _OutlineWidth);

                return o;
            }
            
            fixed4 frag (v2f i) : SV_Target {
                return _color;
            }
            ENDCG
        }
    }
}
```



© Unity Technologies Japan/UC
