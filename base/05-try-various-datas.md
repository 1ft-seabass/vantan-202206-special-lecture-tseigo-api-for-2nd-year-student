# いろいろなデータにつないでみよう

この章は、柴犬画像 API 体験の進み具合で、調整するパートです。

わたしが今回用に作ったいろいろなデータにつないでみます。

## Unity Hub で新しいプロジェクト

![image](https://i.gyazo.com/66d21dad134c483d99e51b56010f2028.png)

Unity Hub で新しいプロジェクトボタンをクリックします。

![image](https://i.gyazo.com/5bfab3113a939a88c8480f098bb84383.png)

テンプレートは 3D コアを選択して

- プロジェクト名
    - API-Various

を設定したらプロジェクトを作成します。

## 表示オブジェクトの準備

![image](https://i.gyazo.com/dd6f7eb48a844ec4d4ad842a609c5eca.png)

空の GameObject で DisplayObject と名前をつけます。

![image](https://i.gyazo.com/5f69b0895d20c9914befc7acbdaa09c9.png)

DisplayObject で右クリックして、 3D Object > Text - TextMeshPro をクリックします。

![image](https://i.gyazo.com/21c8e3cdebd0216ad465e6418f5f0e65.png)

TextMeshPro まわりのインポーター TMP Importer が出てくるので Import TMP Essentials ボタンをクリック。いろいろ読み込まれたら TMP Importer ウィンドウを閉じます。

![image](https://i.gyazo.com/d48f9502ead47cdbb411eb523bdbefca.png)

Text (TMP) が登場します。

![image](https://i.gyazo.com/bccf149b35e1bc383df056f7d654a174.png)

`TextAPI` に名前を変更します。

![image](https://i.gyazo.com/df8f5162489a18e851d3619f31f70671.png)

テキストを選択して Rect Transform を以下のように変更します。

- Width
  - 50
- Height
  - 20
- Scale
  - X 0.3
  - Y 0.3
  - Z 0.3

![image](https://i.gyazo.com/76e9d22390a78f64e8b77e90c9a641da.png)

Alignment を、縦軸・横軸両方中央に。

![image](https://i.gyazo.com/3547aa41fd8cc106330d4ded4d76acb1.png)

Main Camera をクリック。

![image](https://i.gyazo.com/1e00994dae2cf8398e9c4118e28ab825.png)

- Background
  - Solid Color
- カラー
  - 黒

に、見やすさのためにしておきます。

![image](https://i.gyazo.com/d71611411e21f089fe9ea50d06b5ff16.png)

Scene ビューでテキストを見やすく配置します。

## API 読み込み部分を作る

![image](https://i.gyazo.com/b776c0e4808230e4e3e7567ddedc557a.png)

これからいろいろなデータを読むために `APILoader` という名前でひとつ空の GameObject をつくります。

![image](https://i.gyazo.com/a56e0ca417e1609a9a43148d26d37886.png)

そして `APILoader` ができたら選択して Add Component の操作で同名の APILoader スクリプトを作っておきます。

![image](https://i.gyazo.com/aa35f34bc024f641a9e1f294325422e7.png)

Assets のルートの階層に APILoader のスクリプトファイルが出てくるので、ダブルクリックします。

![image](https://i.gyazo.com/00c9c8150d3f8dbfcd82bec6e0a3979d.png)

ここに書き込んでいきます。

## OuraRing という私についてる指輪健康トラッカーデバイス

![image](https://i.gyazo.com/7ae286ec99fdb6e9c00dd1b1d8f9dc3e.jpg)

指につけるデバイスで色々なセンサーで健康状態を常にチェックしてくれる。

![image](https://i.gyazo.com/7538080de22acca7b33b20fc87bd5016.jpg)

スマホアプリでBluetooth でデータ取得し体調・睡眠時間・アクティビティ・心拍数などクラウド分析できる。

![image](https://i.gyazo.com/df117f9043634daadd3f9d0bcba511f6.png)

そして API が揃ってる。→ https://cloud.ouraring.com/v2/docs#tag/Heart-Rate

![image](https://i.gyazo.com/ed7d394aa28e3767ad6e25ba5db1341d.png)

そのまま取得しちゃうと、私のトークンとかばれちゃってマズいので、Node-RED というローコード連携ツールを間に挟んで、良い感じにみんなが一時的に触れるように。

こんなデータが来ます。

```js
{
bpm: 83,
source: "awake",
timestamp: "2022-06-22T13:17:56+00:00",
timestampJST: "2022-06-22T22:17:56+09:00",
timestampJSTShort: "06/22 22:17"
}
```

APILoader のスクリプトは、こちらをコピーアンドペーストしましょう。

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using TMPro;


public class APILoader : MonoBehaviour
{
    // JSON データ化するクラス
    [Serializable]
    public class OuraRingData
    {
        public int bpm;
        public string source;
        public string timestampJSTShort;
        public string timestampJST;
        public string timestamp;
    }

    void Start()
    {
        // 一度実行ならこれ
        // StartCoroutine(GetAPICore());

        // 今回は定期実行
        InvokeRepeating("GetAPI", 1.0f, 1.0f);
    }

    // StartCoroutine でアクセスできるようにするため
    void GetAPI()
    {
        StartCoroutine(GetAPICore());
    }

    IEnumerator GetAPICore()
    {
        // API URL
        UnityWebRequest request = UnityWebRequest.Get("https://vantan-tc-tseigo-2022-22bqvc.herokuapp.com/ouraring/data");

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

            // そのうえで JSON データ化
            OuraRingData data = JsonUtility.FromJson<OuraRingData>(json);

            // TextAPI に反映
            GameObject.Find("TextAPI").GetComponent<TextMeshPro>().text = string.Format("HeartRate : {0}\nGetTime: {1}", data.bpm, data.timestampJSTShort);
        }


    }
}

```

スクリプトを保存できたら Play ボタンをクリックして試してみましょう。

![image](https://i.gyazo.com/04d91165da02807b6109872b65ff1332.png)

## この部屋の CO2 をリアルタイム取得

![image](https://i.gyazo.com/ea1a4371bd4ae4fd02dc77bddde27b67.jpg)

この部屋の CO2 の量を M5Stack というデバイスと Grove CO2 センサーを仕掛けています。

![image](https://i.gyazo.com/d58a72547830b9006a95e4d0508108e0.jpg)

これも Node-RED を間に挟んで、良い感じにみんなが一時的に触れるように。

こんなデータが来ます。

```js
{
CO2: 455.0577,
Temperature: 29.63569,
Humidity: 64.44244,
type: "sensor"
}
```

APILoader のスクリプトは、こちらをコピーアンドペーストしましょう。

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using TMPro;


public class APILoader : MonoBehaviour
{
    // JSON データ化するクラス
    [Serializable]
    public class CO2SensorData
    {
        public float CO2;
        public float Temperature;
        public float Humidity;
        public string type;
    }

    void Start()
    {
        // 一度実行ならこれ
        // StartCoroutine(GetAPICore());

        // 今回は定期実行
        InvokeRepeating("GetAPI", 1.0f, 1.0f);
    }

    // StartCoroutine でアクセスできるようにするため
    void GetAPI()
    {
        StartCoroutine(GetAPICore());
    }

    IEnumerator GetAPICore()
    {
        // API URL
        UnityWebRequest request = UnityWebRequest.Get("https://vantan-tc-tseigo-2022-22bqvc.herokuapp.com/sensor/data");

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

            // そのうえで JSON データ化
            CO2SensorData data = JsonUtility.FromJson<CO2SensorData>(json);

            // TextAPI に反映
            GameObject.Find("TextAPI").GetComponent<TextMeshPro>().text = string.Format("M5Stack+Grove\nCO2 : {0}", (int)data.CO2);

            // 2000 ppm 越えたら文字を赤くする
            if (data.CO2 > 2000)
            {
                GameObject.Find("TextAPI").GetComponent<TextMeshPro>().color = Color.red;
            } else
            {
                GameObject.Find("TextAPI").GetComponent<TextMeshPro>().color = Color.white;
            }
        }


    }
}

```

スクリプトを保存できたら Play ボタンをクリックして試してみましょう。

![image](https://i.gyazo.com/50d487d05ccde35438dfe30a23dfe6e6.png)

ppm 2000 までは白文字で表示されます。

![image](https://i.gyazo.com/f1492abc02078e6612814a001d55dfe4.png)

2000 を越えると赤文字で表示されます。

## obniz という IoT デバイスに取り付けたテープ LED の色が変化する

![image](https://i.gyazo.com/6db132d5a6c0638c9898c37a10786487.jpg)

obniz という IoT デバイスに取り付けたテープ LED があります。

![image](https://i.gyazo.com/db513a28aa595dc3337f4e045663cc10.png)

これも Node-RED を間に挟んで、良い感じにみんなが一時的に触れるように。

みんなでアクセスしまくるので、誰がどう変えたかは分かりにくいですが、変化します！

こんなデータを送りましょう。

```js
{
  "name":"tseigo"
}
```

`tseigo` の部分を半角英数字で自分の名前を設定してみましょう。

APILoader のスクリプトは、こちらをコピーアンドペーストしましょう。

```csharp
using System;
using System.Text;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using TMPro;


public class APILoader : MonoBehaviour
{
    // JSON データ化するクラス
    [Serializable]
    public class SendData
    {
        public string name;
    }

    void Start()
    {
        // 一度実行ならこれ
        GetAPI();
    }

    // StartCoroutine でアクセスできるようにするため
    void GetAPI()
    {
        StartCoroutine(GetAPICore());
    }

    IEnumerator GetAPICore()
    {
        // API URL
        // 今回は POST リクエストの設定
        // green のほかに、red、blue もいけます
        UnityWebRequest request = new UnityWebRequest("https://vantan-tc-tseigo-2022-22bqvc.herokuapp.com/obniz/green", UnityWebRequest.kHttpVerbPOST);
        // SendData クラス呼び出し
        SendData data = new SendData();
        // 自分の名前を半角英数字で設定
        data.name = "--";
        // SendData クラスに合わせて JSON データ変換
        string json = JsonUtility.ToJson(data);
        // UTF8 変換
        byte[] postData = Encoding.UTF8.GetBytes(json);
        // 送るデータの設定
        request.uploadHandler = new UploadHandlerRaw(postData);
        // JSON で送るヘッダー設定
        request.SetRequestHeader("Content-Type", "application/json");
        // 受信データの受け皿を準備
        request.downloadHandler = new DownloadHandlerBuffer();

        yield return request.SendWebRequest();

        if (request.isNetworkError || request.isHttpError)
        {
            Debug.Log(request.error);

            GameObject.Find("TextAPI").GetComponent<TextMeshPro>().text = "NG";
        }
        else
        {
            // 結果をテキストとして表示します
            Debug.Log(request.downloadHandler.text);

            GameObject.Find("TextAPI").GetComponent<TextMeshPro>().text = "OK";
        }


    }
}

```

スクリプトを保存できたら Play ボタンをクリックして試してみましょう。

![image](https://i.gyazo.com/274deece434c7e2c2216419d34cfd3ee.png)

送信がうまくいけば OK という文字が表示されます。

## 質疑応答

![image](https://i.gyazo.com/aba8ccd625e7320883851b71ebd0caf2.png)

ここまでで質問があればどうぞ！

## 次にすすみましょう

左のナビゲーションから「API を旅してみよう」にすすみましょう。