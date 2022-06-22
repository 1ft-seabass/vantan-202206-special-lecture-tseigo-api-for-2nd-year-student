# API と何かをつくるときの TIPS

## Unity で試行錯誤するのは大変

ここまでつないできましたが、Unity で C# を書きながら HTTP リクエストを試行錯誤するのは正直大変です。

ですので、別のツールを使って成功させてから Unity に置き換えていくことがおススメです。

## Postman

https://www.postman.com/

![image](https://i.gyazo.com/1ae2334c8c9b4e6f6b26539fb665284d.png)

HTTP リクエストを色々試すなら Postman はおススメです。このような UI で色々試すことができます。

## Visua Studio Code 拡張機能 REST Client

![image](https://i.gyazo.com/333620f49b9478d49688443ce2640393.png)

Visua Studio Code を使ってる方なら、拡張機能 REST Client も扱いやすいです。

参考資料
- VS Code上でHTTPリクエストを送信し、VS Code上でレスポンスを確認できる「REST Client」拡張の紹介 - Qiita
  - https://qiita.com/toshi0607/items/c4440d3fbfa72eac840c

## Node-RED

https://nodered.jp/

![image](https://i.gyazo.com/6340fb604bf3dcddfbe58994d05a57d7.jpg)

JavaScript(node.js) でできている、少ないプログラムでブロックをつないで GUI で仕組みが作れるローコードツールで、ブラウザベースのエディタによってパレットに並ぶ多種多様なノードを結びつけて用意にフローを作成できます。もちろん、HTTP による API 接続にも強いです。

私の場合は、Unity 開発時には、自分の PC にインストールしローカルホストの形で Unity からアクセスして色々な制作をやりやすくしています。

## eneblar

https://www.enebular.com/ja/

![image](https://i.gyazo.com/212ec86944d9389dc0864539749ba676.jpg)

今回も教材の一部で使っていました。Node-RED を外部サーバー配置できたり、実行できる環境を用意する仕組みが簡単に作れます。

## 上級編 : 各社クラウドサービス

![image](https://i.gyazo.com/105b684fdc12d90b601a44365f741ce6.png)

 AWS・GCP・Azure・IBM Cloud など各社クラウドサービスを利用すると、APIによって様々な機能を使うことができ、みなさんの Unity の仕組みをパワフルにサポートしてくれるでしょう。

## そのほかいろいろ

時間が許せば

- Microsoft Azure
  - 12 か月間無料のサービス、いつでも無料の 25 個以上のサービス、200 米国ドルのクレジット
    - https://azure.microsoft.com/ja-jp/free/
- HTTPBin
  - https://httpbin.org/
- Airtable
  - https://airtable.com/
- Twitter API よくなった
  - Twitter API v2 の Essential Access ができた手順メモ – 1ft-seabass.jp.MEMO
    - https://www.1ft-seabass.jp/memo/2022/02/16/twitter-essential-access/
- API 連携サービス
  - integromat(Make)
    - integromatがサポート終了。Makeへのアップデートと注意点について - Qiita
      - https://qiita.com/cal_mgc/items/51fe79c26a8604f9828a

などなど

## 質疑応答

![image](https://i.gyazo.com/aba8ccd625e7320883851b71ebd0caf2.png)

ここまでで質問があればどうぞ！

## 次にすすみましょう

左のナビゲーションから「事例紹介・質疑応答」にすすみましょう。