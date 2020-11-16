# インストール手順 Windows編

対象OS: Windows 10

## 1. Chromeのインストール

[https://www.google.com/chrome/](https://www.google.com/chrome/)からダウンロードしてください。

## 2. VSCodeのインストール

[https://azure.microsoft.com/ja-jp/products/visual-studio-code/](https://azure.microsoft.com/ja-jp/products/visual-studio-code/)からダウンロードしてください。

とくに必要なプラグインはありませんが、便利そうなプラグインや見やすいテーマがあればインストールしてください。  
メニューの日本語化はお勧めしません。
## 3. Node.js のインストール

[https://nodejs.org/ja/](https://nodejs.org/ja/)  から **推奨版(LTS)** をダウンロードしインストールします。

* **Tools Native Modules**画面の `Automatically install the necessary tools` チェックはONにしないで下さい (デフォルトはOFFです)

インストール後コマンドプロンプトを起動し、でバージョンを確認

```shell
node -v
```

※ プロンプトは省略しました

## 4. インストール確認用Reactプロジェクトの作成

コマンドプロンプトで以下を実行します。

~~~shell
mkdir ReactWorks
cd ReactWorks
npx create-react-app hello_world --template cra-template-ey-office
cd hello_world
npm start
~~~

※ プロンプトは省略しました

ブラウザーが `http://localhost:3000` をアクセスし、ブラウザー画面に `Hello Word!`  が表示されれば完了です！