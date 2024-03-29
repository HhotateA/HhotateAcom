---
title: 波長ベース色空間
layout: default
---

![659afc172f18b7f95b8b82d68341a168.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/90244b49-18eb-f45b-f20f-ee17e5728014.png)

# 概要
　色の表現方法にはRGBやHSVなどがあり、これらは人間が色を認識するL・M・S錐体がそれぞれ赤・緑・青に反応することに基づいて色を表現しています。
　たしかにRGBやHSVは人間の目に映るあらゆる色を表現できるのですが、この空間で色を変化させても不規則に明るさが変化したりしてそんなにきれいには見えないと思います。
　そこで今回は光の波長をRGBに変換する関数を紹介します。これを使ってきれいな虹色を表現してください！
![92e6dbfe950f8c631cbfb4519fed6132.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/fefcd68c-dd15-1359-5866-0170a0112095.png)

# コード
波長とRGBの対応表はCIE1931色空間(http://cvrl.ucl.ac.uk/cmfs.htm) に基づいています。
このコードはShaderLabですが書き換えればGLSLでもHLSLでも使えるはず。。。

## HLSL

```c#:Wave2RGB.cginc
//data on http://cvrl.ucl.ac.uk/cmfs.htm
static float MinVal = 390.0;
static int DataNum = 89;
static float Interval = 5.0;
static fixed4 CMF[89]={ //fixed4(wavelength,R,G,B)元のデータの構造由来
    fixed4(390,1.5000E-03,-4.0000E-04,6.2000E-03),
    fixed4(395,3.8000E-03,-1.0000E-03,1.6100E-02),
    fixed4(400,8.9000E-03,-2.5000E-03,4.0000E-02),
    fixed4(405,1.8800E-02,-5.9000E-03,9.0600E-02),
    fixed4(410,3.5000E-02,-1.1900E-02,1.8020E-01),
    fixed4(415,5.3100E-02,-2.0100E-02,3.0880E-01),
    fixed4(420,7.0200E-02,-2.8900E-02,4.6700E-01),
    fixed4(425,7.6300E-02,-3.3800E-02,6.1520E-01),
    fixed4(430,7.4500E-02,-3.4900E-02,7.6380E-01),
    fixed4(435,5.6100E-02,-2.7600E-02,8.7780E-01),
    fixed4(440,3.2300E-02,-1.6900E-02,9.7550E-01),
    fixed4(445,-4.4000E-03,2.4000E-03,1.0019E+00),
    fixed4(450,-4.7800E-02,2.8300E-02,9.9960E-01),
    fixed4(455,-9.7000E-02,6.3600E-02,9.1390E-01),
    fixed4(460,-1.5860E-01,1.0820E-01,8.2970E-01),
    fixed4(465,-2.2350E-01,1.6170E-01,7.4170E-01),
    fixed4(470,-2.8480E-01,2.2010E-01,6.1340E-01),
    fixed4(475,-3.3460E-01,2.7960E-01,4.7200E-01),
    fixed4(480,-3.7760E-01,3.4280E-01,3.4950E-01),
    fixed4(485,-4.1360E-01,4.0860E-01,2.5640E-01),
    fixed4(490,-4.3170E-01,4.7160E-01,1.8190E-01),
    fixed4(495,-4.4520E-01,5.4910E-01,1.3070E-01),
    fixed4(500,-4.3500E-01,6.2600E-01,9.1000E-02),
    fixed4(505,-4.1400E-01,7.0970E-01,5.8000E-02),
    fixed4(510,-3.6730E-01,7.9350E-01,3.5700E-02),
    fixed4(515,-2.8450E-01,8.7150E-01,2.0000E-02),
    fixed4(520,-1.8550E-01,9.4770E-01,9.5000E-03),
    fixed4(525,-4.3500E-02,9.9450E-01,7.0000E-04),
    fixed4(530,1.2700E-01,1.0203E+00,-4.3000E-03),
    fixed4(535,3.1290E-01,1.0375E+00,-6.4000E-03),
    fixed4(540,5.3620E-01,1.0517E+00,-8.2000E-03),
    fixed4(545,7.7220E-01,1.0390E+00,-9.4000E-03),
    fixed4(550,1.0059E+00,1.0029E+00,-9.7000E-03),
    fixed4(555,1.2710E+00,9.6980E-01,-9.7000E-03),
    fixed4(560,1.5574E+00,9.1620E-01,-9.3000E-03),
    fixed4(565,1.8465E+00,8.5710E-01,-8.7000E-03),
    fixed4(570,2.1511E+00,7.8230E-01,-8.0000E-03),
    fixed4(575,2.4250E+00,6.9530E-01,-7.3000E-03),
    fixed4(580,2.6574E+00,5.9660E-01,-6.3000E-03),
    fixed4(585,2.9151E+00,5.0630E-01,-5.3700E-03),
    fixed4(590,3.0779E+00,4.2030E-01,-4.4500E-03),
    fixed4(595,3.1613E+00,3.3600E-01,-3.5700E-03),
    fixed4(600,3.1673E+00,2.5910E-01,-2.7700E-03),
    fixed4(605,3.1048E+00,1.9170E-01,-2.0800E-03),
    fixed4(610,2.9462E+00,1.3670E-01,-1.5000E-03),
    fixed4(615,2.7194E+00,9.3800E-02,-1.0300E-03),
    fixed4(620,2.4526E+00,6.1100E-02,-6.8000E-04),
    fixed4(625,2.1700E+00,3.7100E-02,-4.4200E-04),
    fixed4(630,1.8358E+00,2.1500E-02,-2.7200E-04),
    fixed4(635,1.5179E+00,1.1200E-02,-1.4100E-04),
    fixed4(640,1.2428E+00,4.4000E-03,-5.4900E-05),
    fixed4(645,1.0070E+00,7.8000E-05,-2.2000E-06),
    fixed4(650,7.8270E-01,-1.3680E-03,2.3700E-05),
    fixed4(655,5.9340E-01,-1.9880E-03,2.8600E-05),
    fixed4(660,4.4420E-01,-2.1680E-03,2.6100E-05),
    fixed4(665,3.2830E-01,-2.0060E-03,2.2500E-05),
    fixed4(670,2.3940E-01,-1.6420E-03,1.8200E-05),
    fixed4(675,1.7220E-01,-1.2720E-03,1.3900E-05),
    fixed4(680,1.2210E-01,-9.4700E-04,1.0300E-05),
    fixed4(685,8.5300E-02,-6.8300E-04,7.3800E-06),
    fixed4(690,5.8600E-02,-4.7800E-04,5.2200E-06),
    fixed4(695,4.0800E-02,-3.3700E-04,3.6700E-06),
    fixed4(700,2.8400E-02,-2.3500E-04,2.5600E-06),
    fixed4(705,1.9700E-02,-1.6300E-04,1.7600E-06),
    fixed4(710,1.3500E-02,-1.1100E-04,1.2000E-06),
    fixed4(715,9.2400E-03,-7.4800E-05,8.1700E-07),
    fixed4(720,6.3800E-03,-5.0800E-05,5.5500E-07),
    fixed4(725,4.4100E-03,-3.4400E-05,3.7500E-07),
    fixed4(730,3.0700E-03,-2.3400E-05,2.5400E-07),
    fixed4(735,2.1400E-03,-1.5900E-05,1.7100E-07),
    fixed4(740,1.4900E-03,-1.0700E-05,1.1600E-07),
    fixed4(745,1.0500E-03,-7.2300E-06,7.8500E-08),
    fixed4(750,7.3900E-04,-4.8700E-06,5.3100E-08),
    fixed4(755,5.2300E-04,-3.2900E-06,3.6000E-08),
    fixed4(760,3.7200E-04,-2.2200E-06,2.4400E-08),
    fixed4(765,2.6500E-04,-1.5000E-06,1.6500E-08),
    fixed4(770,1.9000E-04,-1.0200E-06,1.1200E-08),
    fixed4(775,1.3600E-04,-6.8800E-07,7.5300E-09),
    fixed4(780,9.8400E-05,-4.6500E-07,5.0700E-09),
    fixed4(785,7.1300E-05,-3.1200E-07,3.4000E-09),
    fixed4(790,5.1800E-05,-2.0800E-07,2.2700E-09),
    fixed4(795,3.7700E-05,-1.3700E-07,1.5000E-09),
    fixed4(800,2.7600E-05,-8.8000E-08,9.8600E-10),
    fixed4(805,2.0300E-05,-5.5300E-08,6.3900E-10),
    fixed4(810,1.4900E-05,-3.3600E-08,4.0700E-10),
    fixed4(815,1.1000E-05,-1.9600E-08,2.5300E-10),
    fixed4(820,8.1800E-06,-1.0900E-08,1.5200E-10),
    fixed4(825,6.0900E-06,-5.7000E-09,8.6400E-11),
    fixed4(830,4.5500E-06,-2.7700E-09,4.4200E-11),
};

fixed4 Wave2RGB(float wavelength){ //波長→RGB
    wavelength = clamp( wavelength, MinVal, MinVal+Interval*DataNum);
    int index = clamp( (int)(wavelength-MinVal)/Interval, 0, DataNum);
    float weight = saturate((wavelength - MinVal - index * Interval)/(Interval));
    fixed4 output = lerp(CMF[index],CMF[index+1],weight);
    return fixed4(output.yzw,1.0);
}

fixed4 Val2RGB(float val){ //01→RGB
    return Wave2RGB(val*300+390);
}
```

```c#:wave2color.shader
Shader "HOTATE/Wave2Color" {
    Properties {
        _Wave ("wave(nm)",range(390,830)) = 500
    }
    SubShader {
        Tags { "Queue" = "Geometry" "RenderType" = "Opaque"}

        Pass {
            CGPROGRAM
                #pragma vertex vert
                #pragma fragment frag
                #include "UnityCG.cginc"
                #include "wave2rgb.cginc"

                float _Wave;

                struct appdata {
                    float4 vertex : POSITION;
                    float2 uv : TEXCOORD;
                };

                struct v2f {
                    float4 vertex : SV_POSITION;
                    float2 uv : TEXCOORD;
                };

                v2f vert (appdata IN) {
                    v2f output;
                    output.vertex = UnityObjectToClipPos(IN.vertex);
                    output.uv = IN.uv;
                    return output;
                }

                fixed4 frag(v2f IN) : SV_Target {
                    return Wave2RGB(_Wave);
                    //return Val2RGB(IN.uv.x);
                }
            ENDCG
        }
    }
}
```

```c#:Rainbow.shader
Shader "HOTATE/Rainbow" {
    Properties {
        _Ypow ("Y曲げ具合",range(0.0,5000.0)) = 1000.0
        _Xpow ("X曲げ具合",range(0.0,5000.0)) = 100.0
        _Height ("高さ",range(-5000,5000)) = 0.0
        _Brightnes ("明るさ",range(0,30)) = 1.0
    }
    SubShader {
        Tags { "Queue" = "Transparent"}
        Blend One One

        Pass {
            CGPROGRAM
                #pragma vertex vert
                #pragma fragment frag
                #include "UnityCG.cginc"
                #include "wave2rgb.cginc"

                float _Ypow, _Xpow, _Height, _Brightnes;

                struct appdata {
                    float4 vertex : POSITION;
                    float2 uv : TEXCOORD;
                };

                struct v2f {
                    float4 vertex : SV_POSITION;
                    float2 uv : TEXCOORD;
                };

                v2f vert (appdata IN) {
                    v2f output;
                    output.vertex = UnityObjectToClipPos(IN.vertex);
                    output.uv = IN.uv;
                    return output;
                }

                fixed4 frag(v2f IN) : SV_Target {
                    return Wave2RGB(IN.uv.y*_Ypow + (IN.uv.x-0.5)*(IN.uv.x-0.5)*_Xpow + _Height) * _Brightnes;
                }
            ENDCG
        }
    }
}
```

## GLSL

```glsl:wave2rgb
```

![e1dc2f6d8c60262c9926dcaee8bdeea4.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/aa6cc329-2d58-5773-3b89-cd6b9ce7744f.gif)

# 解説
　多段階線形補完みたいな方法使っているので、これは他にも応用できそう・・・
　波長方式だと単純にはループさせられないので、ここはひと工夫必要かと思います。

# サンプル
虹サンプルのgithubリポジトリ

https://github.com/HhotateA/RainbowColor_Shaderlab
![35dfee577643676b7d3d25c32807308e.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/392903/7eb397ed-a1e3-8a40-914a-c8b004610880.png)
