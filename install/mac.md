# インストール手順 Mac編

対象OS: macOS 10.15(10.14)

## Node.js のインストール

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