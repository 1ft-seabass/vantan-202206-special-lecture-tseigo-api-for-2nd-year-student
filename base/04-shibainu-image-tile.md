# API から柴犬画像を表示

## この章ですすめること

![image](https://i.gyazo.com/19445804d0012399e8bed0743b77896a.jpg)

柴犬画像 API にはつながったので、いよいよ 3D に呼び出して表示をしてみます。

## Plane を一つ作成して配置

柴犬画像を 1 つ貼り付ける Plane を配置します。

![image](https://i.gyazo.com/de138c24e588168bd682d4512ee02100.png)

Hierarchy で Plane を一つ配置します。

![image](https://i.gyazo.com/5ccfe04d7800c4b2443161645905c4f7.png)

名前は Plane1 にします。

![image](https://i.gyazo.com/900eaf8ea20d37436ee378b79d1b64df.png)

Transform はこのように設定します。

### Plane が茶色い人は

![image](https://i.gyazo.com/2b659e6b0cc52b30fcb57fbbb2c86bba.png)

Plane が茶色い人は、

![image](https://i.gyazo.com/33248a2f4a26dbbe22ccd91589c2403d.png)

右下にある Auto Generate Lighting : Off をクリックします。

![image](https://i.gyazo.com/519666f8072227053c18fa2b10ba1b1f.png)

Debug Setting で Auto Generate をチェックします。

![image](https://i.gyazo.com/83101542ddc8d66627ab69611d598b7b.png)

このように。

![image](https://i.gyazo.com/dd5fb06aad339251e626efbde756bb2a.png)

白くライトが復活します。

## ShibainuAPILoader を上書き更新

以下のプログラムで上書きします。

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;

public class ShibainuAPILoader : MonoBehaviour
{

    // JSON データ化するクラス
    [Serializable]
    public class ShibaData
    {
        public List<string> urls;
    }

    void Start()
    {
        StartCoroutine(GetShibainu());
    }

    IEnumerator GetShibainu()
    {
        // 3 つの柴犬画像を取得するURL
        UnityWebRequest request = UnityWebRequest.Get("http://shibe.online/api/shibes?count=3&urls=true&httpsUrls=true");

        yield return request.SendWebRequest();

        if (request.isNetworkError || request.isHttpError)
        {
            Debug.Log(request.error);
        }
        else
        {
            // 結果をテキストとして表示します
            Debug.Log(request.downloadHandler.text);

            // まずテキストデータとして取得
            string json = (string)request.downloadHandler.text;

            Debug.LogFormat("json: {0}", json);

            // ルートが配列なので JSON 化する前に urls オブジェクトで包んで調整する
            json = "{\"urls\":" + json + "}";

            // そのうえで ShibaData クラスで JSON データ化
            ShibaData shibaData = JsonUtility.FromJson<ShibaData>(json);

            // 3 つの柴犬画像 URL をリストアップ
            Debug.LogFormat("1: {0}", shibaData.urls[0]);
            Debug.LogFormat("2: {0}", shibaData.urls[1]);
            Debug.LogFormat("3: {0}", shibaData.urls[2]);

            // 画像を反映
            StartCoroutine(GetImageToTexture("Plane1", shibaData.urls[0]));
        }


    }

    // 画像 URL から画像を読み込んで Plane にテクスチャとして貼り付ける
    IEnumerator GetImageToTexture(string objName, string loadURL)
    {
        UnityWebRequest www = UnityWebRequestTexture.GetTexture(loadURL);

        //画像を取得できるまで待つ
        yield return www.SendWebRequest();

        if (www.isNetworkError || www.isHttpError)
        {
            Debug.Log(www.error);
        }
        else
        {
            // 取得した画像のテクスチャをRawImageのテクスチャに張り付ける
            Texture texture = ((DownloadHandlerTexture)www.downloadHandler).texture;
            GameObject.Find(objName).GetComponent<MeshRenderer>().material.SetTexture("_MainTex", texture);
        }
    }

}
```

## Play してみる

![image](https://i.gyazo.com/aa765e1b788e9ada4845a1e838cecda9.png)

プログラムが反映できた、Play ボタンをクリックして試してみます。

![image](https://i.gyazo.com/173623ae97cb26c0f3f21bb8b58cbb16.jpg)

上手く反映できていれば、このように 1 番目の柴犬画像が Plane にテクスチャとして貼り付けられます！

## 加わったスクリプトの解説

加わった部分の解説です。

```csharp
// 画像を反映
StartCoroutine(GetImageToTexture("Plane1", shibaData.urls[0]));
```

まず、柴犬画像 URL を `GetImageToTexture` という関数に渡して、画像 URL から画像を読み込んで Plane にテクスチャとして貼り付けることをしています。

```csharp
// 画像 URL から画像を読み込んで Plane にテクスチャとして貼り付ける
IEnumerator GetImageToTexture(string objName, string loadURL)
{
    UnityWebRequest www = UnityWebRequestTexture.GetTexture(loadURL);

    //画像を取得できるまで待つ
    yield return www.SendWebRequest();

    if (www.isNetworkError || www.isHttpError)
    {
        Debug.Log(www.error);
    }
    else
    {
        // 取得した画像のテクスチャをRawImageのテクスチャに張り付ける
        Texture texture = ((DownloadHandlerTexture)www.downloadHandler).texture;
        GameObject.Find(objName).GetComponent<MeshRenderer>().material.SetTexture("_MainTex", texture);
    }
}
```

実際に動いている `GetImageToTexture` です。`DownloadHandlerTexture` でダウンロードしたデータをテクスチャとして扱って、MeshRenderer にテクスチャを貼り付けています。

## 3 匹の柴犬に増やしてみる

![image](https://i.gyazo.com/977a12c8bf3bb7c99481f82e77ea3fee.png)

まず、Plane1 を選択してコピーして 2 回ペーストします。

![image](https://i.gyazo.com/daba106872f73b82f87eb0d5b49dae61.png)

Plane2, Plane3 にリネームします。

![image](https://i.gyazo.com/89d27adbc5ba6a7ced435a6dea982468.png)

Plane2 の Transform をこのようにします。 Position X を 1 にします。

![image](https://i.gyazo.com/769ef57fdc8f2cb7ead3fdff96735223.png)

Plane3 の Transform をこのようにします。 Position X を 7 にします。

![image](https://i.gyazo.com/7d9d04b70c6ce54133e766caa6464638.png)

このように配置されました。

ShibainuAPILoader を以下のように変更します。

```csharp
// 3 つの柴犬画像 URL をリストアップ
Debug.LogFormat("1: {0}", shibaData.urls[0]);
Debug.LogFormat("2: {0}", shibaData.urls[1]);
Debug.LogFormat("3: {0}", shibaData.urls[2]);

// 画像を反映
StartCoroutine(GetImageToTexture("Plane1", shibaData.urls[0]));
StartCoroutine(GetImageToTexture("Plane2", shibaData.urls[1]));  // 加えた
StartCoroutine(GetImageToTexture("Plane3", shibaData.urls[2]));  // 加えた
```

設定できたら Play してみましょう！

![image](https://i.gyazo.com/19445804d0012399e8bed0743b77896a.jpg)

うまく設定できていれば、このように 3 柴犬表示されているはずです！

## HoloLens2 による柴犬表示デモ

![image](https://i.gyazo.com/059f208b6e4765bbaea4dd52d181ea1e.jpg)

このプログラムを HoloLens 2 にちょっと調整してビルドしたデモをお見せします。

![image](https://i.gyazo.com/6cc2628dac21f2e36e0505c107daa34f.jpg)

柴犬に囲まれるはず。

このように、Oculus Quest や HoloLens 2 のようなデバイスでも API を活かすことができます。

## 質疑応答

![image](https://i.gyazo.com/aba8ccd625e7320883851b71ebd0caf2.png)

ここまでで質問があればどうぞ！

## 次にすすみましょう

左のナビゲーションから「いろいろなデータにつないでみよう」にすすみましょう。