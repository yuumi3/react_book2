# インストール手順 Windows編

対象OS: Windows 10


## Node.js のインストール

[https://nodejs.org/ja/](https://nodejs.org/ja/)  から **推奨版(LTS)** をダウンロードしインストールします

* **Tools Native Modules**画面の `Automatically install the necessary tools` チェックはONにしないで下さい (デフォルトはOFFです)

インストール後コマンドプロンプトでバージョンを確認

```shell
node -v
```

※ プロンプトは省略しました

## create-react-appのインストール

~~~shell
npm install --global create-react-app
~~~

※ プロンプトは省略しました

## インストール確認用Reactプロジェクトの作成

~~~shell
mkdir ReactWorks
cd ReactWorks
create-react-app hello_world --template cra-template-ey-office
cd hello_world
npm start
~~~

※ プロンプトは省略しました

ブラウザーが `http://localhost:3000` をアクセスし、ブラウザー画面に `Hello Word!`  が表示されれば完了です！