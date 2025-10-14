# iOS/Android での買い切りアイテム実装（広告非表示アイテム）<p>
## 使用環境
Unity:2022.3.44f1<p>
参考にしたサイト：

## 概要
1. Unity IAPのセットアップ
2. 広告削除アイテムの登録
3. 購入処理スクリプトの作成
4. 購入状態の保存と広告表示ロジックの制御
5. UI(購入ボタン)の作成

## 1.UnityIAPのセットアップ<p>
document.mdを参照<p>

## 2.広告削除アイテムの登録<p>
document.mdを参照<p>

## 3.購入処理スクリプトの作成
IAPManager.cs

~~~
using UnityEngine;
using UnityEngine.Purchasing;

// IStoreListenerインターフェースを実装する必要があります
public class IAPManager : MonoBehaviour, IStoreListener
{
    public static IAPManager Instance { get; private set; }

    // IAPコントローラーと拡張機能
    private IStoreController storeController;
    private IExtensionProvider storeExtensionProvider;

    // --- アイテムID ---
    // IAP Catalogで設定したIDと一致させる
    public const string ProductIdRemoveAds = "remove_ads";

    // 広告が削除されたかどうかを判定するフラグ
    public bool IsAdsRemoved { get; private set; }

    void Awake()
    {
        // シングルトンの設定
        if (Instance == null)
        {
            Instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
            return;
        }

        // 購入状態をロードする
        LoadPurchaseState();

        // IAPの初期化
        InitializePurchasing();
    }

    /// <summary>
    /// IAPの初期化処理
    /// </summary>
    private void InitializePurchasing()
    {
        var builder = ConfigurationBuilder.Instance(StandardPurchasingModule.Instance());

        // IAP Catalogで登録した商品を追加
        builder.AddProduct(ProductIdRemoveAds, ProductType.NonConsumable);

        UnityPurchasing.Initialize(this, builder);
    }

    /// <summary>
    /// 初期化成功時に呼ばれる
    /// </summary>
    public void OnInitialized(IStoreController controller, IExtensionProvider extensions)
    {
        storeController = controller;
        storeExtensionProvider = extensions;

        // 初期化時に購入済みかチェックする
        CheckNonConsumableItems();
    }

    /// <summary>
    /// 購入成功時に呼ばれる
    /// </summary>
    public PurchaseProcessingResult ProcessPurchase(PurchaseEventArgs args)
    {
        string productId = args.purchasedProduct.definition.id;

        if (string.Equals(productId, ProductIdRemoveAds, System.StringComparison.Ordinal))
        {
            Debug.Log("広告削除アイテムの購入に成功しました！");
            // 広告削除処理を実行
            RemoveAds();
        }
        else
        {
            Debug.LogWarning($"購入成功しましたが、未定義のアイテムです: {productId}");
        }

        // 購入処理の完了をIAPに通知
        return PurchaseProcessingResult.Complete;
    }

    /// <summary>
    /// 広告削除アイテムの購入を開始する
    /// </summary>
    public void BuyRemoveAds()
    {
        if (storeController == null)
        {
            Debug.LogError("IAPが初期化されていません．");
            return;
        }
        storeController.InitiatePurchase(ProductIdRemoveAds);
    }

    /// <summary>
    /// 購入情報の復元（リストア）を開始する (主にiOSで必要)
    /// </summary>
    public void RestorePurchases()
    {
        if (storeController == null)
        {
            Debug.LogError("IAPが初期化されていません．");
            return;
        }

        // Apple App Store, Google Play, etc.
        var apple = storeExtensionProvider.GetExtension<IAppleExtensions>();
        apple?.RestoreTransactions(result => {
            Debug.Log($"リストア処理の結果: {result}");
            // 必要に応じてリストア後のUI更新などをここで行う
        });
    }


    // --- 広告削除のロジック ---

    private void RemoveAds()
    {
        IsAdsRemoved = true;
        // 購入状態を保存
        PlayerPrefs.SetInt("IsAdsRemoved", 1);
        PlayerPrefs.Save();

        // ここで広告を非表示にする処理を直接呼び出す（例）
        // AdManager.Instance.HideBannerAd();
    }
    
    /// <summary>
    /// 非消耗アイテムが購入済みかチェックする
    /// </summary>
    private void CheckNonConsumableItems()
    {
        var product = storeController.products.WithID(ProductIdRemoveAds);
        if (product != null && product.hasReceipt)
        {
            Debug.Log("広告削除アイテムは既に購入済みです．");
            RemoveAds();
        }
    }
    
    /// <summary>
    /// デバイスから購入状態をロードする
    /// </summary>
    private void LoadPurchaseState()
    {
        // PlayerPrefsから読み込み．0はデフォルト値
        IsAdsRemoved = PlayerPrefs.GetInt("IsAdsRemoved", 0) == 1;
    }


    // --- 失敗時の処理 ---

    public void OnInitializeFailed(InitializationFailureReason error)
    {
        Debug.LogError($"IAPの初期化に失敗しました: {error}");
    }

    public void OnPurchaseFailed(Product product, PurchaseFailureReason failureReason)
    {
        Debug.LogError($"アイテム「{product.definition.id}」の購入に失敗しました．理由: {failureReason}");
    }
}
~~~

このスクリプトを空のGameObjectにアタッチして，シーンに配置してください．

## 4.購入状態の保存と広告表示ロジックの制御
購入状態はアプリを終了しても保持される必要があります．上記のスクリプトでは，最も簡単なPlayerPrefsを使用してデバイスに保存しています．

### 広告表示側の制御
広告を表示するスクリプト(AdManager.cs)側で，広告を表示する前IAPManagerのフラグをチェックするように修正します．

~~~
// 広告表示スクリプトの例
public class AdManager : MonoBehaviour
{
    public void ShowBannerAd()
    {
        // IAPManagerのフラグをチェックし、購入済みなら広告を表示しない
        if (IAPManager.Instance != null && IAPManager.Instance.IsAdsRemoved)
        {
            Debug.Log("広告は購入済みのため表示しません．");
            return;
        }
        
        // --- ここにバナー広告を表示する処理を記述 ---
    }
    
    public void ShowInterstitialAd()
    {
        // 同様にフラグをチェック
        if (IAPManager.Instance != null && IAPManager.Instance.IsAdsRemoved)
        {
            Debug.Log("広告は購入済みのため表示しません．");
            return;
        }

        // --- ここにインタースティシャル広告を表示する処理を記述 ---
    }
}
~~~
アプリ起動時に IAPManagerが購入状態をPlayerPrefsから復元し，Is AdsRemovedフラグを更新するため，この
チェックだけで広告表示を制御できます．

## 5.UI(購入ボタン)の作成
最後にユーザがアイテムを購入するためのボタンを配置します．
1. Canvas上にButtonを作成します．
2. ボタンのInspectorウィンドで，On Click（）イベントの＋をクリックします．
3.  IAP ManagerがアタッチされているGameObjectをドラック&ドロップします．
4.  関数のプルダウンメニューから IAP Manager＞Buy RemoveAds（）を選択します．
これで，ボタンをクリックすると購入処理が開始されるようになります．
 


