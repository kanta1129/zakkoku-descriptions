## App登録方法
まず，左下のplayersetting<p>
設定ファイル
- Company Name：あなたの会社名やサークル名，個人名などを入力します（例: MyCompany）．

- Product Name: アプリの名前を入力します（例: Counter App）．

- Package Name (Android) / Bundle Identifier (iOS): アプリの固有IDです．通常，com.会社名.アプリ名 の形式で設定します．他のアプリと重複しないように注意してください．(例：com.mycompany.counterapp)<p>

### Android編
file>build setting
Androidを選択し，Switch Platformを押す
Buildボタンを押し，保存先とファイル名を指定
.apkファイルが作成されるのでｍこれをAndroidの実機にインストール

ここからストア登録方法


### IOS編
file>build setting
iosを選択し，Switch Platformを押す
Buildボタンを押し，保存先とファイル名を指定
XcodeプロジェクトをMacで開き，実機テストやAppStoreに申請ができる．

### App Store Connectでアプリの箱を作る
- サインイン
- MyAppを選択