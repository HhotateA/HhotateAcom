---
title: 【Unity】GoogleDriveからVRMファイルをインポートする
layout: default
---

#概要
VRoidHubに頼りたくないとき用
#こんなかんじ
こんなかんじ
<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">関係ないけど猫になりたい <a href="https://t.co/Iyx2bu6yJ3">pic.twitter.com/Iyx2bu6yJ3</a></p>&mdash; ほたて猫魔人 (@HhotateA_xR) <a href="https://twitter.com/HhotateA_xR/status/1158605196080979968?ref_src=twsrc%5Etfw">August 6, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

#コード

```c#:VRMDownloader.cs
using UnityEngine;
using System.Collections;
using UnityEngine.Networking;
using VRM;

public class VRMDownloader : MonoBehaviour {
    [SerializeField] private string _URL {get;set;}
    [SerializeField] private GameObject _LoadingIcon;
    [SerializeField] private GameObject _MissingIcon;
    public GameObject VRMRoot;
    void Start(){
        if(_LoadingIcon==null) _LoadingIcon = new GameObject();
        if(_MissingIcon==null) _MissingIcon = new GameObject();
    }

    public void LoadVRMfromInput(){
        string url = _URL;
        StartCoroutine(URL2VRM(GoogleUrl(url)));
    }
    public void LoadVRMfromURL(string url){
        StartCoroutine(URL2VRM(GoogleUrl(url)));
    }
    public string GoogleUrl(string url)
    {
        if (url == null || url == "") return url;
        if (url.StartsWith("https://drive.google.com/open?id="))
        {
            url = url.Remove(0, 33);
        }
        else if (url.StartsWith("https://drive.google.com/file/d/"))
        {
            url = url.Remove(0, 32);
            if (url.EndsWith("/view?usp=sharing")){
                url = url.Remove(url.Length - 17);
            }else if(url.EndsWith("/view?usp=drivesdk")){
                url = url.Remove(url.Length - 18);
            }else if(url.EndsWith("/view")){
                url = url.Remove(url.Length - 5);
            }
        }
        else if (url.StartsWith("https://drive.google.com/uc?id="))
        {
            url = url.Remove(0, 31);
        }
        else
        {
            return url;
        }
        return "https://drive.google.com/uc?id=" + url;
    }
    private IEnumerator URL2VRM(string url){
        missingIcon.SetActive(false);
        loadingIcon.SetActive(true);

        UnityWebRequest www = UnityWebRequest.Get(url);
        www.timeout = 10;
        yield return www.Send();

        if (www.isNetworkError || www.isHttpError)
        {
            Debug.Log(www.error);
        }
        else
        {
            Debug.Log(www.downloadHandler.text);
        }
        Destroy(VRMRoot);
        try{
            VRMRoot = VRMImporter.LoadFromBytes(www.downloadHandler.data);
        }catch{
            Debug.Log("MissingURL");
            missingIcon.SetActive(true);
        }
        loadingIcon.SetActive(false);
    }
}
```
#使いかた
 LoadVRMfromURL(string url)を呼び出す。
#工夫したとこ
googleのURLを変換した。
