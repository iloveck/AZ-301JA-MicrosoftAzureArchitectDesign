﻿# メッセージング サービスを使用した Azure ソリューション コンポーネントの統合

# ラボの回答キー: Azure リソース間の通信を支援するメッセージング コンポーネントのデプロイ

## 始める前に

1. 次の資格情報を使用して Windows 10 ラボ仮想マシンにログインしていることを確認します。

    - ユーザー名: **Admin**

    - パスワード: **Pa55w.rd**

1. Windows 10 デスクトップの下部にあるタスク バーを確認します。タスク バーには、ラボで使用する一般的なアプリケーションのアイコンが含まれています。

    - Microsoft Edge

    - File Explorer

    - [Visual Studio Code](https://code.visualstudio.com/)

    - [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

    - Windows上のUbuntu上のBash

    - Windows PowerShell 

    > **注記** これらのアプリケーションへのショートカットは、**スタート メニュー** でも検索できます

## 演習 1: サービス バスの名前空間をデプロイ

#### タスク 1: Azure Portal を開く

1. タスク バーで、マイクロソフト **Microsoft Edge** アイコンをクリックします。

1. 開いているブラウザ ウィンドウで、 **Azure Portal**(<https://portal.azure.com>)に移動します。

1. プロンプトが表示された場合は、このラボで使用する Azure サブスクリプションの所有者役割を持つユーザー アカウントを使用して認証します。

#### タスク 2: Service Bus 名前空間を作成

1. Azure Portal の左上隅にある **リソースの作成** をクリックします。 

1. **新規** ブレード上部の **マーケットプレースの検索** テキスト ボックスで、**Service Bus** と入力し、**Enter** を押します。

1. **すべて** ブレードで、検索結果から **Service Bus** をクリックします。

1. **Service Bus** ブレードで、**作成** ボタンをクリックします。

1. **名前空間を作成** ブレードで、次のタスクを実行します。

    - **名前** テキスト ボックスに、グローバルに一意の名前を入力します。 

    - **価格レベル** ドロップダウン リストで、**基本** オプションを選択します。 
    
    - **サブスクリプション** ドロップダウン リスト エントリは、既定値に設定したままにします。 

    - **リソース グループ** セクションで **新規作成** オプションが選択されていることを確認し、テキスト ボックスに **AADesignLab1101-RG** と入力します。   

    - **ロケーション** ドロップダウン リストで、このラボでリソースをデプロイする Azure リージョンを選択します。 

    - **作成** ボタンをクリックします。

1. プロビジョニングが完了するのを待ってから、次の手順に進みます。 

#### タスク 3: Service Bus キューの作成

1. Azure Portal のハブ メニューで、**リソース グループ** をクリックします。

1. **リソース グループ** ブレードで、**AADesignLab1101-RG** をクリックします。

1. **AADesignLab1101-RG** ブレードで、新しく作成されたService Busの名前空間をクリックします。 

1. 「Service Busの名前空間」 ブレードの **エンティティ** セクションで、**キュー** をクリックします。   

1. 「Service Busの名前空間」 ブレードで、**+ キュー** ボタンをクリックします。 

1. **キューの作成** ペインで、次のタスクを実行します。 

    - **名前** テキスト ボックスで、 **メッセージ** と入力します。

    - 残りの設定はすべてデフォルト値のままにしておきます。

    - **作成** ボタンをクリックします。

#### タスク 4: Service Bus 接続文字列の取得

1. 「Service Busの名前空間」 ブレードに戻り、**共有アクセス ポリシー** をクリック します。 

1. 「Service Busの名前空間」 ブレードで、 **RootManageSharedAccessKey** をクリックします。 

1. **SAS ポリシー** では、次の操作を行います。**RootManageSharedSharedAccessKey** ペインを表示し、**プライマリ接続文字列** フィールドの値を見つけて記録します。この値は、この演習の後半で使用します。

> **復習**: この演習では、新しい Service Bus 名前空間を作成し、名前空間内のキューにアクセスするための接続文字列を記録しました。

## 演習 2: ロジック アプリを作成する

#### タスク 1: Azure ストレージ アカウントを作成する

1. Azure Portal の左上隅にある **リソースの作成** をクリックします。

1. **新規** ブレードの上部にある **マーケットプレイスの検索** テキスト ボックスで、 **ストレージ アカウント** と入力し、**Enter** を押します。

1. **すべて** のブレードで、検索結果から  **ストレージ アカウント** をクリックします。   

1. **ストレージ アカウント** ブレードで、 **作成** ボタンをクリックします。   

1. **ストレージ アカウントの作成** ブレードで、次のタスクを実行します。

    - **名前** テキスト ボックスに、3 - 24の小文字および数字の組み合わせで構成される一意の名前を入力します。
    
    - **デプロイモデル** セクションで、 **リソース マネージャ** オプションが選択されていることを確認します。 

    - **アカウントの種類** ドロップダウン リストで、 **ストレージ (汎用 v1)** オプションが選択されていることを確認します。 

    - この演習で前に選択したのと同じ Azure リージョンに **ロケーション** エントリを設定したままにします。

    - **レプリケーション** ドロップダウン リストで、 **ローカル冗長ストレージ (LRS)** エントリを選択します。 

    - **パフォーマンス** セクションで、 **標準** オプションが選択されていることを確認します。  

    - **安全な必要が必要** セクションで、 **無効化** オプションが選択されていることを確認します。  

    - **サブスクリプション** ドロップダウン リスト エントリは、既定値に設定したままにします。

    - **リソース グループ** セクションで **既存のリソースの使用** オプションが選択されていることを確認し、以下のドロップダウン リストで、この演習で前に作成したリソース グループを選択します。   

    - **仮想ネットワークの構成**　オプションは、既定値に設定したままにします。 

    - **階層名前空間** オプションは既定値に設定したままにします。 

    - **作成** ボタンをクリックします。

1. プロビジョニングが完了するのを待ってから、次の手順に進みます。

1. Azure Portal のハブ メニューで、**リソース グループ** をクリックします。

1. **リソース グループ** ブレードで、**AADesignLab1101-RG** をクリックします。

1. **AADesignLab1101-RG** ブレードで、新しく作成されたストレージ アカウントをクリックします。 

1. 「ストレージ アカウント」 ブレードで、 **Blobs** タイルをクリックします。 

1. 「ストレージ アカウント」 ブレードで、 **+コンテナー** ボタンをクリックします。 

1. **新規コンテナー** ペインで、次のタスクを実行します。 

    - **名前** テキスト ボックスで、**messageoutput** と入力します。

    - **パブリック アクセスレベル**  ドロップダウン リストで、**Blob (BLOB の場合のみの匿名読み取りアクセス)** オプションを選択します。

    - **OK** ボタンをクリックします。

#### タスク 2: ロジック アプリを作成する

1. **新規** ブレードの上部の **マーケットプレイスの検索**  テキスト ボックスで、**Logic App** と入力し、**Enter** キーを押します。 

1. **すべて** ブレードで、検索結果の  **ロジック アプリ** をクリックします。   

1. **ロジック アプリ** ブレードで、 **作成** ボタンをクリックします。   

1. **ロジック アプリの作成** ブレードで、次のタスクを実行します。

    - **名前** テキスト ボックスで、 **ServiceBusWorkflow**.と入力します。
    
    - **サブスクリプション** ドロップダウン リスト エントリは、既定値に設定したままにします。

    - **「リソース グループ」** セクションで **既存のリソースの使用** オプションを選択し、ドロップダウン リストで **AADesignLab1101-RG** を選択します。

    - **ロケーション** ドロップダウン リストで、前のタスクで選択したのと同じ Azure リージョンを選択します。  

    - **ログ分析** セクションで、**オフ** ボタンが選択されていることを確認します。 

    - **作成** ボタンをクリックします。

1. 次のタスクに進む前に、プロビジョニングが完了するのを待ちます。 

#### タスク 3: ロジック アプリの手順を構成します。

1. Azure Portal のハブ メニューで、**リソースグループ** をクリックします。

1. **リソース グループ** ブレードで、**AADesignLab1101-RG** をクリックします。

1. **AADesignLab1101-RG** ブレードで、前のタスクで作成したロジック アプリを表すエントリをクリックします。 

1. **ロジック アプリ デザイナー** ブレードで、下にスク役割し、**テンプレート** セクションの **「空白のロジック アプリ」** タイルをクリックします。     

1. **ロジック アプリ デザイナー** ブレードで、次のタスクを実行します。 

    - **検索コネクタとトリガー** テキスト ボックスで、  **Service Bus** と入力します。

    - 検索結果で、 **キュー (自動完了)でメッセージを受信したときに - Service Bus** という名前のトリガーを選択します。 

    - **接続名** テキスト ボックスに、 **ServiceBusConnection** と入力します。
    
    - **Service Bus 名前空間** の一覧で、この実習ラボで前に作成した名前空間を選択します。 

    - ポリシーの一覧で、 **ルート管理共有アクセスキー** ポリシーを選択します。 

    - **作成** ボタンをクリックします。

1. **キューメッセージを受信した場合(自動完了)** のステップでは、次のタスクを実行します。

    - **キュー名** ドロップダウン リストで、 **メッセージ** エントリを選択します。 

    - **間隔** テキスト ボックスで、typ **30** と入力します。

    - **頻度** ドロップダウン リストで、 **2 番目のエントリ** を選択します。

1. **ロジック アプリ デザイナー** ブレードで、 **+新しいステップ** ボタンをクリックします。   

1. **ロジック アプリ デザイナー** ブレードで、次のタスクを実行します。 

    - **コネクタとアクションの検索** のテキスト ボックスに **Storage blob** と入力します。

    - 検索結果で、 **Blob の作成 - Azure Blob Storage** という名前のアクションを選択します。

    - **接続名** テキスト ボックスに、 **StorageConnection** と入力します。
    
    - *ストレージ アカウント* の一覧で、この実習ラボで前に作成したアカウントを選択します。 

    - **作成** ボタンをクリックします。  

1. **Blob の作成** 手順で、次のタスクを実行します。

    - **フォルダパス** テキスト ボックスに **/messageoutput** と入力します。

    - **Blob 名** のテキスト ボックスに、**@concat(triggerBody()?「'MessageId'」, '.txt')** と入力します。

    - **Blob コンテンツ** テキスト ボックスに、 **@string(decodeBase64(triggerBody()?「'ContentData'」))** と入力します。

1. **ロジック アプリ デザイナー** ブレードの上部にある  **保存** ボタンをクリックしてワークフローを保持します。   

#### タスク 2: Cloud Shell を開く

1. ポータルの上部にある **Cloud Shell** アイコンをクリックして、新しいシェル インスタンスを開きます。 

    > **注記**  **Cloud Shell** アイコンは、 *より大きい* 文字と *アンダースコア* 文字の組み合わせで構成されるシンボルです。 

1. サブスクリプションを使用して **Cloud Shell** を初めて開く場合は、初めて使用する **Cloud Shell** を構成するウィザードが表示されます。  プロンプトが表示された場合は、 **Azure Cloud Shellへようこそ** ペインで、 **Bash (Linux)** をクリックします。

    > **注記** **Cloud Shell** の構成オプションが表示されない場合は、このコースの演習で既存のサブスクリプションを使用している可能性が高いと考えられます。  その場合は、次のタスクに直接進みます。 

1. **ストレージにマウントされていない** ペインで、 **詳細設定を表示** をクリックし、次のタスクを実行します。   

    - **サブスクリプション** ドロップダウン リスト エントリは、既定値に設定したままにします。

    - **Cloud Shell リージョン** ドロップダウン リストで、Azure リージョンの一致を選択するか、このラボでリソースをデプロイしたロケーションの近くを選択します。 

    - **リソース グループ** セクションで **既存のリソースの使用** オプションを選択し、ドロップダウン リストで **AADesignLab1101-RG** を選択します。

    - **ストレージ アカウント** セクションで **新規作成** オプションが選択されていることを確認し、下のテキスト ボックスに、3 - 24の小文字および数字の組み合わせで構成される一意の名前を入力します。 

    - **ファイル共有** セクションで **新規作成** オプションが選択されていることを確認し、下のテキスト ボックスに **cloudshell** を入力します。

    - **ストレージの作成** ボタンを選択します。

1. **Cloud Shell** が初回セットアップ手順を完了するのを待ってから、次のタスクに進みます。 

#### タスク 4: Validate Logic App using Node.js

1. ポータルの上部にある **Cloud Shell** アイコンをクリックして、新しいシェル インスタンスを開きます。

1. ポータルの下部にある  **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、 **Enter** キーを押し、NPM を使用して **azure** パッケージをインストールします。   

    ```sh 
	npm install azure
    ```

1. **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、 **Enter** キーを押して対話型ノード　ターミナルを開きます。   

    ```sh
    node
    ```

1. **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、 **Enter** キーを押してノードの **「azure」** モジュールをインポートします。   

    ```sh
    var azure = require('azure');
    ```
    > **注記** 出力には **未定義** が表示されます。これは予測されていることです。。
    
1. **Cloud Shell** コマンド プロンプトで、次のコマンド (プレースホルダ `<Service Bus namespace connection string>`をこの演習で前に記録した URL の値に置き換える) を入力し、 **Enter** キーを押して新しい変数を作成します。

    ```sh
    var connectionString = '<Service Bus namespace connection string>';
    ```

1. **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、 **Enter** キーを押して、Service Bus 名前空間に接続する新しいクライアントを作成します。   

    ```sh
    var serviceBusService = azure.createServiceBusService(connectionString);
    ```

1. **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、**Enter** キーを押して、クライアントを使用して Service Bus 名前空間キューにメッセージを送信します。   

    ```sh
    serviceBusService.sendQueueMessage('messages', { body: 'Hello World' }, function(error) { console.log(error) });    
    ```

1. Azure Portal のハブ メニューで、**リソース グループ** をクリックします。

1. **「リソース グループ」** ブレードで、**AADesignLab1101-RG** をクリックします。

1. **AADesignLab1101-RG** ブレードで、この実習ラボで前述した Azure Storage アカウントをクリックします。 

1. 「ストレージ アカウント」 ブレードで、 **Blobs** タイルをクリックします。 

1. 「ストレージ アカウント コンテナー」 ブレードで、 **メッセージ出力** コンテナーをクリックします。 

1. コンテナーに新しく作成された blob に注意してください。

> **復習**: この演習では、Service Bus 名前空間内のキューからのメッセージによってトリガーされるロジック アプリを作成しました。

## 演習 3: ラボ リソースの削除

#### タスク 1: Cloud Shell を開く

1. ポータルの上部にある **Cloud Shell** アイコンをクリックして、「Cloud Shell」 ペインを開きます。 

1. **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、 **Enter** キーを押して、この実習ラボで作成したすべてのリソース グループを一覧表示します。   

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab11')]".name --output tsv
    ```

1. 出力に、この実習ラボで作成したリソース グループのみが含まれていることを確認します。これらのグループは、次のタスクで削除されます。

#### タスク 2: リソース グループの削除

1. **Cloud Shell** コマンド プロンプトで、次のコマンドを入力し、 **Enter** キーを押してこの実習ラボで作成したリソース グループを削除します。

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab11')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

1. ポータルの下部にある **Cloud Shell** プロンプトを閉じます。 

> **復習**: この演習では、この演習で使用したリソースを削除しました。
