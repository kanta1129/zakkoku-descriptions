# App登録方法(新しいアプリを審査まで持っていく流れ)
まず， Unity上で，file>build setting を開きます．<p>
- Company Name：あなたの会社名やサークル名，個人名などを入力します（例: MyCompany）．
入力する箇所<p>
- Product Name: アプリの名前を入力します（例: Counter App）．
- Package Name (Android) / Bundle Identifier (iOS): アプリの固有IDです．通常，com.会社名.アプリ名 の形式で設定します．他のアプリと重複しないように注意してください．(例：com.mycompany.counterapp)<p>

## Android編
file>build setting
Androidを選択し，Switch Platformを押す<p>
player Settingの設定<p>

- Package Name (パッケージ名): アプリの固有IDです。com.会社名.アプリ名 の形式で、全世界でユニークなIDを設定します（例: com.kanta.MyNiceApp）。
- Version: ユーザーに見えるバージョン名です（例: 1.0）。
- Bundle Version Code: ストアが新旧を判断するための内部的な番号です。アプリを更新するたびに、必ず1ずつ増やしてください（例: 1 → 2）。
- Target API Level: Googleが推奨する最新レベルに設定するのが望ましいです。Automatic (highest installed)にしておくのが簡単です。<p>

キーストア(Keystore)の作成<p>
これは**「このアプリは確かにあなたが作りました」ということを証明するデジタル署名**のファイルです。非常に重要で、一度失くすとアプリの更新ができなくなります。<p>
Player Settings > Publishing Settings を開きます。<p>
Keystore Manager... をクリックし、Keystore... > Create New > Anywhere を選択します。<p>
保存場所を指定し、パスワードとエイリアス（署名の名前）、エイリアスのパスワードを設定します。このパスワードは絶対に忘れないでください。<p>
作成が完了すると、Publishing Settingsの画面でキーストアとエイリアス、パスワードが自動で入力されます。<p>


## IOS編
file>build setting
iosを選択し，Switch Platformを押す
Buildボタンを押し，保存先とファイル名を指定
XcodeプロジェクトをMacで開き，実機テストやAppStoreに申請ができる．

### 1.App Store Connectでアプリの箱を作る

App Store Connectにサインイン<p>
MyAppを選択<p>
内容<p>
- プラットフォーム: 「iOS」にチェックを入れます。
- 名前: App Storeに表示されるアプリ名です．既に使用されている名前は使えない（例: すごいカウンターアプリ）。
- プライマリ言語: 「日本語」を選択します。
- バンドルID: UnityやXcodeで設定したcom.yourcompany.yourappname形式のIDをドロップダウンから選択します。（ここで表示されない場合、Xcodeでの設定がApple側にまだ認識されていない可能性があります）
- SKU: あなたが管理するための任意のIDです。ユーザには表示されない内部トラッキング用にアプリに与える一意のID。分かりやすいようにバンドルIDと同じ文字列を入れておくのが一般的です（例: com.kanta.MyNiceApp）。アカウントにアプリを追加した後は，SKU変更不可。
- ユーザアクセス: 「フルアクセス」のままでOKです。

これでアプリの「箱」ができました。次にこの箱に、Xcodeからアプリ本体をアップロードします。

<div align="center">
  <img src="images/AppRegi/1.png" alt="アプリの箱作成">
</div>

### 2.Xcodeでビルドとアーカイブを行う

Xcodeで，ビルド対象デバイスの変更:<p>
Xcodeの画面上部、再生（▶）ボタンの右隣にあるデバイス選択の部分をクリックします。<p>
これまでiPhone実機などを選んでいたと思いますが、リストの一番上にある「Any iOS Device (arm64)」を選択してください。<p>

Macのメニューバーから Product -> Archive を選択します。<p>

ビルドが開始され、成功すると「Organizer」という新しいウィンドウが自動で開きます。ここに、今作成されたアプリのアーカイブが表示されます。<p>

<div align="center">
  <img src="images/AppRegi/2.png" alt="アプリの箱作成">
</div>

### 3.Xcodeからビルドをアップロード
Organizerウィンドウを使って、作成したアーカイブをApp Store Connectに送ります。<p>
アップロードを開始:<p>
Organizerウィンドウで、先ほど作成したアーカイブが選択されていることを確認し、右側にある青い「Distribute App」ボタンをクリックします。<p>
配布方法の選択:<p>
配布方法を聞かれるので、「App Store Connect」が選択されていることを確認し、「Next」をクリックします。<p>

<div align="center">
  <img src="images/AppRegi/3.png" alt="アプリの箱作成">
</div>

アップロード先を選択:<p>
「Upload」が選択されていることを確認し、「Next」をクリックします。<p>

署名の設定:(きちんとXcodeとApp Store Connectがつながっていれば出てこない予想)<p>
「Automatically manage signing」にチェックが入っていれば、Xcodeが自動でストア提出用の署名を行います。そのまま「Next」をクリックしてください。<p>
アップロードの実行:
サマリーが表示されるので、内容を確認して右下の「Upload」ボタンをクリックします。<p>

アップロードが開始されます。アプリのサイズによっては数分〜数十分かかることがあります。<p>

"Successfully uploaded" と表示されれば完了です！<p>

### 4.App Store Connectで審査を提出する
アップロードが完了したら、App Store Connect側でビルドが処理されるのを待ちます（数分〜1時間程度）。その後、最終的な情報を入力して審査に出します。<p>
ビルド(実際にStoreに出るアプリの内容)＋提出物(審査に必要な要項やストアに出す内容)→提出のイメージ<p>
1. ビルドの選択:<p>
App Store Connectのアプリ情報ページに戻り、「ビルド」のセクションで、先ほどアップロードしたビルドを選択できるようになっているか確認します。<p>

<div align="center">
  <img src="images/AppRegi/4.png" alt="ビルドの画面">
</div>

2. 提出情報の入力:<p>

- アプリの審査には以下の情報が必須です。左側のメニューを参考に、すべて埋めていきましょう。
- アプリアイコン: 指定されたサイズのアプリアイコン画像。
- スクリーンショット: iPhoneやiPadの各画面サイズのスクリーンショット画像。
- 説明: アプリの説明文。
- キーワード: 検索でヒットさせたい単語。
- サポートURL: あなたの連絡先やアプリのサポート情報が載っているWebページのURL。
- Appのプライバシー: アプリがユーザーのどの情報を収集し、何に使うのかを申告します。正直に回答してください。

3. 審査へ提出:
すべての必須項目を埋めると、画面右上の「審査へ提出」ボタンが青色に変わります。<p>
これをクリックすると、アプリはAppleの審査チームに送られます。<p>
お疲れ様でした！ 審査には通常1日〜数日かかります。結果はメールで通知されます。 無事承認されることを願っています！<p>