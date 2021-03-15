# インストール手順 Mac編

対象OS: macOS 10.15


## 1. Chromeのインストール

[https://www.google.com/chrome/](https://www.google.com/chrome/)からダウンロードしてください。

## 2. VSCodeのインストール

[https://azure.microsoft.com/ja-jp/products/visual-studio-code/](https://azure.microsoft.com/ja-jp/products/visual-studio-code/)からダウンロードしてください。

とくに必要なプラグインはありませんが、便利そうなプラグインや見やすいテーマがあればインストールしてください。  
メニューの日本語化はお勧めしません。

## 3. Node.js のインストール

#### Homebrewがインストールされている場合

```shell
brew -v
brew update
brew install nodejs
node -v
```

※ プロンプトは省略しました

#### Homebrewがインストールされてない場合

[https://nodejs.org/ja/](https://nodejs.org/ja/)  から **推奨版(LTS)** をダウンロードしインストール

インストール後ターミナルでバージョンを確認

```shell
node --version
```

※ プロンプトは省略しました

#### Proxyの設定が必要な場合

社内のネットワークにFirewall等があり、プロキシサーバーを使う場合はProxyの設定が必要になります。
以下の`SERVER` , `PORT`の部分を社内のプロキシサーバーに置き換えて実行してください。

```shell
npm -g config set proxy "http://SERVER:PORT/"
npm -g config set https-proxy "https://SERVER:PORT/"
npm -g config set registry "http://registry.npmjs.org/"
```
※ プロンプトは省略しました

## 4. インストール確認用Reactプロジェクトの作成

~~~shell
mkdir ReactWorks
cd ReactWorks
npx create-react-app hello_world --template cra-template-ey-office
cd hello_world
npm start
~~~

※ プロンプトは省略しました

npxコマンドを実行したさいに以下のような確認がある場合は`y`をタイプしてください。

```shell
Need to install the following packages:
  create-react-app
Ok to proceed? (y) 
```

ブラウザーが `http://localhost:3000` をアクセスし、ブラウザー画面に `Hello Word!`  が表示されれば完了です！