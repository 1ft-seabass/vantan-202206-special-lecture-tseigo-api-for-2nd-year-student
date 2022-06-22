# 柴犬画像 API につなぐ

## この章ですすめること

![image](https://i.gyazo.com/728ab1bc727950222d256e5db6e4d080.png)

柴犬画像 API につないで、取得した画像 URL をブラウザのアドレスバーにペーストして、アクセスして画像取得を確認します。

![image](https://i.gyazo.com/7ec52a53d8ae79b3906d6fa26d8e991e.jpg)

## 開発環境

- Unity は 2019.4 台
- スクリプトのエディタは Visual Studio

で、すすめます。

## 今回の API

かわいい柴犬画像をランダムに返答してくれる shibe.online という API を使ってみましょう。

![image](https://i.gyazo.com/ffdaa58d8fb284e2d0095d4ba8f60d8b.png)

https://shibe.online/ こちらです。

HTTP リクエストの仕様として読み解くと以下のようになります。

- HTTP リクエスト方法
  - GET メソッド
- アクセスする API の URL
  - http://shibe.online/api/shibes
- 3 つの柴犬画像を取得する場合のパラメータ付き URL
  - http://shibe.online/api/shibes?count=3&urls=true&httpsUrls=true

これのみです。追加で設定すべきパラメータもなく、これは、GET リクエストのためブラウザからアクセスでき、とても扱いやすい API です。

## Unity Hub で新しいプロジェクト

![image](https://i.gyazo.com/66d21dad134c483d99e51b56010f2028.png)

Unity Hub で新しいプロジェクトボタンをクリックします。

![image](https://i.gyazo.com/5bfab3113a939a88c8480f098bb84383.png)

テンプレートは 3D コアを選択して

- プロジェクト名
    - API-Shibainu

を設定したらプロジェクトを作成します。

<img width="389" alt="image.png (2.2 kB)" src="https://img.esa.io/uploads/production/attachments/3062/2022/06/21/8131/9ddf312f-bc49-44b2-9d02-98f55686a8be.png">

作成を待ちます。

![image](https://i.gyazo.com/e82758bd203d860b1158932c904fc7d0.png)

作成されました。

## API を読み込む部分 ShibainuAPILoader GameObject を作っていく

- 柴犬 API に Unity でつなげてみるメモ – 1ft-seabass.jp.MEMO
    - https://www.1ft-seabass.jp/memo/2022/05/07/unity-web-request-for-shiba-online/

私のこちらの記事をベースに作っていきます。

![image](https://i.gyazo.com/d3959983cf68fea851204562dc927299.png)

シーンは Sample Scene のまま、進めます。Hierarchy で Create Empty で空の GameObject を作成します。

![image](https://i.gyazo.com/6a26379d773f9c99bb5de3b530c568ae.png)

名前は `ShibainuAPILoader` とします。

![image](https://i.gyazo.com/95283fcfd6a7289a8c69da07b46fcee0.png)

ShibainuAPILoader をクリックして Inspector に移動して、Add Component をクリックします。

![image](https://i.gyazo.com/f0703235528e197956eedc92371928b7.png)

New script をクリックします。

![image](https://i.gyazo.com/f35bec8bc86b84aebbc0a4304b5caa84.png)

`ShibainuAPILoader` と入力して Create and Add をクリックして新しいスクリプトを作成します。

![image](https://i.gyazo.com/ec0591ade56928fba8914bbce35f5000.png)

Assets のルートの階層に ShibainuAPILoader のスクリプトファイルが出てくるので、ダブルクリックします。

![image](https://i.gyazo.com/a8692371d552ab64f80c852e8e1e5d24.png)

Visual Studio が起動して ShibainuAPILoader が編集できます。

## ShibainuAPILoader プログラムを反映

今回の ShibainuAPILoader のプログラムは、すでに用意してあります。

こちらです。コピーアンドペーストしてスクリプトを保存しましょう。

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
        }


    }

}
```

## Play してみる

![image](https://i.gyazo.com/aa765e1b788e9ada4845a1e838cecda9.png)

プログラムが反映できた、Play ボタンをクリックして試してみます。

![image](https://i.gyazo.com/cbd956b419b9ad81a456820ad0f0a855.png)

うまく設定できていれば、このように Console で

- 結果をテキストとして表示
- 3 つの柴犬画像 URL をリストアップ

の結果が表示され柴犬 API からデータが取得出来ているはずです。

## ためしに 3 番目の画像をブラウザで表示してみましょう

柴犬 API で返される柴犬画像 URL はランダムです。みなさん、それぞれ違う画像が出てきます。

![image](https://i.gyazo.com/728ab1bc727950222d256e5db6e4d080.png)

ためしに 3 番目の画像を Console でコピーします。

![image](https://i.gyazo.com/3fc3a1b3de09f3e92ca4478e22cb2942.png)

ブラウザのアドレスバーにペーストして、アクセスしてみましょう。

![image](https://i.gyazo.com/7ec52a53d8ae79b3906d6fa26d8e991e.jpg)

このように、画像が取得できます

## ShibainuAPILoader プログラムの解説

```csharp
// JSON データ化するクラス
[Serializable]
public class ShibaData
{
    public List<string> urls;
}
```

まず、受け取った JSON データ化するクラスです。 C# で使いやすい構造データに変換するテンプレートの役目です。

これは、受け取るデータ合わせて書き換えるので、いろいろな API を取得するたびに調整する部分です。

```csharp
void Start()
{
    StartCoroutine(GetShibainu());
}
```

まず、起動時に柴犬画像 API に取得に向かいます。

```csharp
    IEnumerator GetShibainu()
    {
        // 3 つの柴犬画像を取得するURL
        UnityWebRequest request = UnityWebRequest.Get("http://shibe.online/api/shibes?count=3&urls=true&httpsUrls=true");

        yield return request.SendWebRequest();
```

まず、`UnityWebRequest` クラスで GET リクエストする API の URL　を設定して、実行します。

```csharp
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
```

`if (request.isNetworkError || request.isHttpError)` で HTTP まわりの取得エラーを検知してます。問題なければ else で取得成功という流れです。

結果を待ってテキストデータとして取得します。

```csharp
            // ルートが配列なので JSON 化する前に urls オブジェクトで包んで調整する
            json = "{\"urls\":" + json + "}";
```

ここがちょっとややこしいんのですが、柴犬 API で返す値のルートが配列なので ArgumentException: JSON must represent an object type. というエラーが出てしまい困りました。FromJson の制約の一つとのことです。

ルートが配列なので JSON 化する前に urls オブジェクトで包んで調整しています。

```csharp
            // そのうえで ShibaData クラスで JSON データ化
            ShibaData shibaData = JsonUtility.FromJson<ShibaData>(json);
```

包んでしまえば、事前に決めた `ShibaData` をもとに `JsonUtility.FromJson` で C# で使いやすい構造データ化します。

```csharp
            // 3 つの柴犬画像 URL をリストアップ
            Debug.LogFormat("1: {0}", shibaData.urls[0]);
            Debug.LogFormat("2: {0}", shibaData.urls[1]);
            Debug.LogFormat("3: {0}", shibaData.urls[2]);
        }


    }

```

あとは、`shibaData.urls` は List 型になるので3つ取得したデータを一つ一つ表示しています。


## 質疑応答

![image](https://i.gyazo.com/aba8ccd625e7320883851b71ebd0caf2.png)

ここまでで質問があればどうぞ！

## 次にすすみましょう

左のナビゲーションから「API から柴犬画像を表示」にすすみましょう。