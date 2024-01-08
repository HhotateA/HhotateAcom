---
title: オブジェクト形状に合ったフィルターをシェーダーで実装する
layout: default
---

![08cb0c17-6b44-47a6-846b-ed561d336179_base_resized.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/69bb9bb3-367b-c7e5-3b3f-71e9abd81581.jpeg)

Unityではシェーダーを自由に記述することで、画面エフェクトのような効果を得ることが出来ます。
しかし、基本的には色に応じた画像効果などが主なるものです。

![813bd51a-9eab-4ff7-b52e-9f1973168a51.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/69d1e477-cbd9-7141-d9e9-e3c81a789c6e.png)

しかし、うまくシェーダーを記述することで、オブジェクト形状に合わせたフィルターを作成することもできます。

![b567fa92-d6bb-4d26-b9ce-d4738f2e3eab_base_resized.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/6756de0b-8fa4-90e4-062a-b0862bd554c6.jpeg)

 Unityでのシェーダーはデプステクスチャを取得できます。
カメラにDepthタイプのRenderTextureをセットすることで、確認することが出来ます。

# 解説

実際にUnityのシーンにカメラを設置して取得できるテクスチャを確認してみましょう。

Unityで新規シーンを作成して、Cubeなどで部屋を作り3Dモデルを配置します。
そしてカメラを配置して、確認してみましょう。

RenderTextureのColorFormatには以下のリンク先の種類があります。
https://docs.unity3d.com/ja/current/ScriptReference/RenderTextureFormat.html
今回は、デフォルトの「ARGB32」と「Depth」で比較します。
![c18444d07a0b098a03e39458e9fa6c9e.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/24b8ffbb-acb7-2a50-8c31-cb2b289f295b.png)
![aas.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/5774205b-271f-5818-629d-2ae43d93b26f.png)
左の「ARGB32」はカラーテクスチャです。通常の写真のようにRGBで取得できるテクスチャです。
右の「Depth」はデプステクスチャです。色が赤ければ近く色が黒ければ遠くにオブジェクトが存在することを表します。


# 実装手順

上の例ではRenderTextureのDepthモードで確認しましたが、シェーダー上からも_CameraDepthTextureからほぼ同じテクスチャが取得できます。
```
            sampler2D _CameraDepthTexture;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.projPos = ComputeScreenPos(o.vertex);
                return o;
            }

            half4 frag (v2f i) : SV_Target
            {
                return LinearEyeDepth(tex2Dproj(_CameraDepthTexture, i.projPos).r);
            }
```
とすることで、各ピクセルのデプスを白黒で取得することが出来ます。

概念としては、図のようにカメラの目前にある板越しに各ピクセルごとにカメラからの距離がわかるようになっています。
![70f8d919d114bcca40c500348da0f292.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/30a6cd27-de6e-5934-5604-519c898caa12.png)

ここで、カメラからフィルターまでのベクトルをさらに延長して、カメラのワールド座標にデプス距離分のベクトルを足すことで、フィルター越しに見えているオブジェクトのワールド座標が割り出せるようになります。
```
            sampler2D _CameraDepthTexture;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.wpos = mul(unity_ObjectToWorld,v.vertex);
                o.projPos = ComputeScreenPos(o.vertex);
                return o;
            }

            half4 frag (v2f i) : SV_Target
            {
                float depth = LinearEyeDepth(tex2Dproj(_CameraDepthTexture, i.projPos).r);
                float3 cameraVec = normalize(i.wpos-_WorldSpaceCameraPos);
                // 赤いオブジェクトのワールド座標 = カメラのワールド座標 + （カメラから青いフィルターまでのベクトル） × 緑のデプス距離
                float3 worldPos = _WorldSpaceCameraPos + cameraVec * depth;
                return half4(worldPos.rgb,1.);
            }
```
![3812c6a475dfdb924ee90ff2e3b5c0fd.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/f65a6bf9-521a-5b41-21df-69e9e288690d.png)

このフィルターでは、フィルターの先のオブジェクトのワールド座標XYZがRGBで表現されています。

# 完成コード
```
			v2f vert (appdata v)
			{
				v2f o;
				o.clipPos = UnityObjectToClipPos(v.vertex);
				o.modelPos = v.vertex;
				return o;
			}

			// Taken from http://answers.unity.com/answers/641391/view.html
			// Creates inverse matrix of input
			float4x4 inverse(float4x4 input)
			{
				#define minor(a,b,c) determinant(float3x3(input.a, input.b, input.c))
				float4x4 cofactors = float4x4(
					minor(_22_23_24, _32_33_34, _42_43_44), 
					-minor(_21_23_24, _31_33_34, _41_43_44),
					minor(_21_22_24, _31_32_34, _41_42_44),
					-minor(_21_22_23, _31_32_33, _41_42_43),

					-minor(_12_13_14, _32_33_34, _42_43_44),
					minor(_11_13_14, _31_33_34, _41_43_44),
					-minor(_11_12_14, _31_32_34, _41_42_44),
					minor(_11_12_13, _31_32_33, _41_42_43),

					minor(_12_13_14, _22_23_24, _42_43_44),
					-minor(_11_13_14, _21_23_24, _41_43_44),
					minor(_11_12_14, _21_22_24, _41_42_44),
					-minor(_11_12_13, _21_22_23, _41_42_43),

					-minor(_12_13_14, _22_23_24, _32_33_34),
					minor(_11_13_14, _21_23_24, _31_33_34),
					-minor(_11_12_14, _21_22_24, _31_32_34),
					minor(_11_12_13, _21_22_23, _31_32_33)
				);
				#undef minor
				return transpose(cofactors) / determinant(input);
			}

			float4x4 INVERSE_UNITY_MATRIX_VP;
			float3 calculateWorldSpace(float4 screenPos)
			{	
				float4 worldPos = mul(INVERSE_UNITY_MATRIX_VP, screenPos);
				float3 worldDir = worldPos.xyz / worldPos.w - _WorldSpaceCameraPos;
				float2 screenUV = screenPos.xy / screenPos.w;
				screenUV.y *= _ProjectionParams.x;
				screenUV = screenUV * 0.5f + 0.5f;
				screenUV = UnityStereoTransformScreenSpaceTex(screenUV);
				float depth = LinearEyeDepth(UNITY_SAMPLE_DEPTH(tex2D(_CameraDepthTexture, screenUV))) / screenPos.w;
				float3 worldSpacePos = worldDir * depth + _WorldSpaceCameraPos;
				return worldSpacePos;
			}

			float calcdepth(float4 screenPos)
			{	
				float4 worldPos = mul(INVERSE_UNITY_MATRIX_VP, screenPos);
				float3 worldDir = worldPos.xyz / worldPos.w - _WorldSpaceCameraPos;
				float2 screenUV = screenPos.xy / screenPos.w;
				screenUV.y *= _ProjectionParams.x;
				screenUV = screenUV * 0.5f + 0.5f;
				screenUV = UnityStereoTransformScreenSpaceTex(screenUV);
				float depth = pow(Linear01Depth(UNITY_SAMPLE_DEPTH(tex2D(_CameraDepthTexture, screenUV))),_level);
				return depth;
			}

			fixed4 frag (v2f i) : SV_Target
			{
				INVERSE_UNITY_MATRIX_VP = inverse(UNITY_MATRIX_VP);
				float4 screenPos = UnityObjectToClipPos(i.modelPos); 
				float2 offset = 1.2 / _ScreenParams.xy * screenPos.w; 
				float3 worldPos1 = calculateWorldSpace(screenPos);
				float3 worldPos2 = calculateWorldSpace(screenPos + float4(0, offset.y, 0, 0));
				float3 worldPos3 = calculateWorldSpace(screenPos + float4(-offset.x, 0, 0, 0));
				float3 worldNormal = normalize(cross(worldPos2 - worldPos1, worldPos3 - worldPos1));
				worldNormal = abs(worldNormal);
				float s = _interval;
				//worldPos1 = abs(worldPos1);
				float2 xuv = fmod(worldPos1.yz,s)/s; xuv.y *= -1; xuv += _XTexscroll.xy * _Time.y;
				float2 yuv = fmod(worldPos1.zx,s)/s; yuv.y *= -1; yuv += _YTexscroll.xy * _Time.y;
				float2 zuv = fmod(worldPos1.xy,s)/s; zuv.y *= -1; zuv += _ZTexscroll.xy * _Time.y;
				fixed4 xcolor = lerp( fixed4(0,0,0,0), tex2D(_XTexture,TRANSFORM_TEX(xuv, _XTexture)).rgba, pow(worldNormal.xxxx,_pow));
				fixed4 ycolor = lerp( fixed4(0,0,0,0), tex2D(_YTexture,TRANSFORM_TEX(yuv, _YTexture)).rgba, pow(worldNormal.yyyy,_pow));
				fixed4 zcolor = lerp( fixed4(0,0,0,0), tex2D(_ZTexture,TRANSFORM_TEX(zuv, _ZTexture)).rgba, pow(worldNormal.zzzz,_pow));
				fixed4 buf = clamp(xcolor+ycolor+zcolor,0,1);

				float2 xeuv = fmod(worldPos1.yz,s)/s; xuv.y *= -1; xeuv += _XEmiscroll.xy * _Time.y;
				float2 yeuv = fmod(worldPos1.zx,s)/s; yuv.y *= -1; yeuv += _YEmiscroll.xy * _Time.y;
				float2 zeuv = fmod(worldPos1.xy,s)/s; zuv.y *= -1; zeuv += _ZEmiscroll.xy * _Time.y;
				fixed4 xecolor = lerp( fixed4(0,0,0,0), tex2D(_XEmission,TRANSFORM_TEX(xeuv, _XEmission)).rgba, pow(worldNormal.xxxx,_pow)) * _XEmiColor * _XEmiPower;
				fixed4 yecolor = lerp( fixed4(0,0,0,0), tex2D(_YEmission,TRANSFORM_TEX(yeuv, _YEmission)).rgba, pow(worldNormal.yyyy,_pow)) * _YEmiColor * _YEmiPower;
				fixed4 zecolor = lerp( fixed4(0,0,0,0), tex2D(_ZEmission,TRANSFORM_TEX(zeuv, _ZEmission)).rgba, pow(worldNormal.zzzz,_pow)) * _ZEmiColor * _ZEmiPower;
				buf += xecolor + yecolor + zecolor;

				float depth = calcdepth(screenPos);
				fixed4 color = lerp( buf * _linecolor, _filter, step(_range,depth));
				return color;
			}

			ENDCG
		}
```
