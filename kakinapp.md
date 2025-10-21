# iOS/Android での買い切りアイテム実装（広告非表示アイテム）<p>
## 使用環境
Unity:2022.3.44f1<p>
参考にしたサイト：

## 概要
1. Unity IAPのセットアップ
2. 広告削除アイテムの登録
3. IAPイベントリスナーの準備
4. 購入処理スクリプトの作成
5. イベントの接続とUIの作成

## 1.UnityIAPのセットアップ<pȘ
UnityIAP.mdを参照<p>

## 2.広告削除アイテムの登録<p>
UnityIAP.mdを参照<p>

## 3.IAPイベントリスナーの準備
v5以降では、IAPの初期化やイベントの待受を専門に行うコンポーネントをシーンに配置します．
1. 空のGameObjectを作成し，IAPManager等わかりやすい名前に
2. 作成したGameObjectを選択し，Add Componentをクリック
3. IAP　Listenerを検索し，アタッチ
このコンポーネントが、IAPの初期化、購入成功、購入失敗といったイベントを自動的に検知してくれます．

## 4.購入処理スクリプトの作成
IAP Listenerから送られてくるイベントを受け取り、実際のロジック（広告削除状態の保存など）を実行するIAPManager.csスクリプトを作成します．<p>
このスクリプトを、先ほど作成したIAPManagerのGameObjectにアタッチしてください．<p>
IAPManager.cs
~~~
using UnityEngine;
using UnityEngine.Purchasing;
using UnityEngine.Purchasing.Extension;

public class IAPManager : MonoBehaviour
{
    public static IAPManager Instance { get; private set; }

    // ▼▼▼【重要】IAP Catalogで自分で設定したIDに必ず変更してください ▼▼▼
    public const string ProductIdRemoveAds = "com.yourcompany.yourgame.removeads"; 

    // 広告が削除されたかどうかを判定するフラグ
    public bool IsAdsRemoved { get; private set; }

    private IStoreController storeController; // StoreControllerを保持する変数を追加

    void Awake()
    {
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
        LoadPurchaseState();
    }

    // IAPの初期化が完了した時にCodelessIAPStoreListenerから呼ばれる
    public void OnIAPInitialized(IStoreController controller, IExtensionProvider extensions)
    {
        Debug.Log("IAPの初期化が完了しました。");
        storeController = controller;
        CheckNonConsumableItems();
    }
    
    // 購入が成功した時にCodelessIAPStoreListenerから呼ばれる
    public void OnPurchaseSuccess(Product product)
    {
        if (product.definition.id == ProductIdRemoveAds)
        {
            Debug.Log("広告削除アイテムの購入に成功しました！");
            RemoveAds();
        }
    }

    // 購入が失敗した時にCodelessIAPStoreListenerから呼ばれる
    public void OnPurchaseFailed(Product product, PurchaseFailureDescription description)
    {
        Debug.LogError($"アイテム「{product.definition.id}」の購入に失敗。理由: {description.reason}, 詳細: {description.message}");
    }

    // UIの購入ボタンからこのメソッドを呼び出します
    public void BuyRemoveAds()
    {
        if (storeController == null)
        {
            Debug.LogError("IAPが初期化されていません。購入処理を開始できません。");
            return;
        }
        storeController.InitiatePurchase(ProductIdRemoveAds);
    }
    
    // UIのリストアボタンからこのメソッドを呼び出します
    public void RestorePurchases()
    {
        // CodelessIAPStoreListenerがリストア処理を管理するため、
        // このボタンは実質的にIAPButtonのリストア機能を使うか、
        // 各ストアの拡張機能を使って手動で呼び出す必要があります。
        Debug.Log("リストア処理を開始します。");
    }

    // --- 内部ロジック (変更なし) ---
    private void RemoveAds()
    {
        IsAdsRemoved = true;
        PlayerPrefs.SetInt("IsAdsRemoved", 1);
        PlayerPrefs.Save();
        Debug.Log("広告削除状態を保存しました。");
    }

    private void CheckNonConsumableItems()
    {
        var product = storeController.products.WithID(ProductIdRemoveAds);
        if (product != null && product.hasReceipt)
        {
            Debug.Log("広告削除アイテムは既に購入済みです。広告削除を適用します。");
            RemoveAds();
        }
    }

    private void LoadPurchaseState()
    {
        IsAdsRemoved = PlayerPrefs.GetInt("IsAdsRemoved", 0) == 1;
    }
}
~~~

## 5.イベントの接続とUIの作成
最後にIAP Listenerが検知したイベントをIAPManagerスクリプトに通知する設定と、UIボタンの作成を行います.<p>
### イベントの接続
1. IAPManagerのGameObjectを選択
2. InspectorウィンドウでIAP Listenerコンポーネントを見つける
3. 以下の３つのイベント覧を設定．

これは途中です...