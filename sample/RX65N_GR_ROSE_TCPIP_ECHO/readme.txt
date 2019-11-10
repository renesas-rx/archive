(1)e2 studio v7.6起動
(2)新規プロジェクト作成
　(A)ファイル→新規→プロジェクト
　(B)C/C++ Project
　　Renesas RX⇒Renesas CC-RX C/C++ Executable Project→次へ
　　プロジェクト名に"rx65n_gr_rose_tcpip" (何でも良い)→次へ
　　ターゲットデバイスの右側"..."ボタンを押して、RX600→RX65N→RX65N - 100pin→R5F565NEHxFP
　　ConfigurationsのHardware Debug構成を生成で、使用するエミュレータを選ぶ→次へ
　　"スマートコンフィグレータを使用する" (Use Smart Configurator) にチェック→終了
　(C)プロジェクトが立ち上がる
　(D)スマートコンフィグレータで設定を施す
　　画面左側のプロジェクトエクスプローラで、 rx65n_gr_rose_tcpip.scfg をダブルクリック
　　エディタウィンドウにスマートコンフィグレータの画面が出る
　　ボートタブを選択
　　　"ボード情報をダウンロードする"を選ぶ (MyRenesasのアカウント情報を入力)
　　　モジュールフォルダパスに表示されているパスをエクスプローラで開く
　　　以下URLにあるGR-ROSE用のボード定義ファイルを入手
　　　https://www.renesas.com/jp/ja/products/gadget-renesas/boards/gr-rose.html#resources
　　　　→e2 studio用ボード定義ファイル(BDF)
　　　モジュールフォルダパスにgr-rose-v1.00.bdfをコピー　(GR-ROSEのBDFはまだ標準配布されてないので手動でインストール必要)
　　　e2 studioを再起動すると、スマートコンフィグレータのボードタブでGR-ROSEが選べるようになる
　　　クロックタブを開いて、逓倍比を10から20にする。
　　コンポーネントタブで"コンポーネントの追加ボタン"を押す
　　　"他のソフトウェアコンポーネントをダウンロード"を押す
　　　RXファミリ RX Driver Package Ver.1.22にチェックをいれてダウンロードボタン　(この操作はすでにダウンロード済みなら不要)
　　　ソフトウェアコンポーネントの選択画面から、以下コンポーネントを選択し終了ボタン
　　　　r_cmt_rx, r_ether_rx, r_sys_time_rx, r_t4_driver_rx, r_t4_rx
　　　コンポーネントタブにおいてr_ether_rx を選択し以下設定変更
　　　　Ethernet interface: MII(Media Independent Interface) -> RMII(Reduced Media Independent Interface)
　　　　The register bus of PHY0 for ETHER0/1: Use ETHER1 -> Use ETHER0
　　　　リソース→ETHERC→ETHER0_RMIIにチェック
　　　　リソース→ETHERC→ETHER0_RMIIの以下端子にチェック
　　　　　REF50CK0, RMII0_TXD_EN, RMII0_TXD1, RMII0_TXD0, RMII0_RXD1, RMII0_RXD0, RMII0_RX_ER, RMII_CRS_DV, ET0_MDC, ET0_MDIO, ET0_LINKSTA
　　　　　補足: ユーザカスタムボードの場合、この後端子タブにて端子設定を行う必要があるが、
　　　　　　　　GR-ROSEやほかの標準ボードの場合はボード情報を読み込みしているため、端子設定は行う必要なし
　　スマートコンフィグレータ画面右上の"コードの生成"を押す
　(E)main()を含むアプリケーションコードを構築する
　　　T4のサンプルプロジェクトをインポートする
　　　　スマートコンフィグレータのコンポーネントタブにおいて、r_t4_rxのところで右クリックして
　　　　"サンプルプロジェクトのダウンロードとインポート"を選択
　　　　　→スマートブラウザの画面で"RXファミリ 組み込み用 TCP/IP M3S-T4-Tiny 導入ガイド"というのが出てくる
　　　　　　→右クリックすると"サンプルコード(プロジェクトのインポート)"が出てくるので選ぶ
　　　　　　　→しばらく待っていると"プロジェクトのインポート"のウィンドウが現れる
　　　　　　　　→選択をすべて解除ボタンを押した後、rskrx65n_2mb_tcp_blockinのサンプルにチェックし終了
　　　　　　　　　→T4のサンプルプロジェクトがインポートできる（プロジェクトエクスプローラに追加される）
　　　プロジェクトエクスプローラでrskrx65n_2mb_tcp_blockingのsrcで右クリック→system explorer
　　　　→tcp_blockingフォルダをコピー
　　　プロジェクトエクスプローラでrx65n_gr_rose_tcpipのsrcで右クリック→system explorer
　　　　→tcp_blockingフォルダをペースト、空のmain()が含まれるrx65n_gr_rose_tcpip.cは不要なので消しておく
　　　プロジェクトエクスプローラからrskrx65n_2mb_tcp_blockingを右クリック→削除で消しておく
　(F)ビルドしてエラーがゼロであることを確認。
(3)実機環境の構築
　エミュレータ接続環境を以下にように構築
　　https://github.com/renesas-rx/amazon-freertos/wiki/%E9%96%8B%E7%99%BA%E7%92%B0%E5%A2%83%E3%82%92%E6%8F%83%E3%81%88%E3%82%8B#%E3%81%AF%E3%82%93%E3%81%A0%E4%BB%98%E3%81%91%E7%92%B0%E5%A2%83%E3%81%8C%E3%81%82%E3%82%8B%E5%A0%B4%E5%90%88
(4)デバッガの設定
　(A)プロジェクトエクスプローラでrx65n_gr_rose_tcpipを右クリック、デバッグ→デバッグの構成を開く
　(B)rx65n_gr_rose_tcpip HardwareDebug を選択、DebuggerタブでConnection Settingsを選択、接続タイプをJTagからFineに変更
　(C)デバッグボタンを押し、デバッガ接続ができることを確認
　　→ダウンロードがうまくいけば、resetprg.c の R_BSP_POR_FUNCTION(R_BSP_STARTUP_FUNCTION) でブレークする
(5)実機動作確認
　(A)GR-ROSEのLANコネクタをDHCP使用可能なネットワークに接続
　(B)e2 studio画面上でRenesas Viewsからデバッグ→Renesas Debug Virtual Consoleを開いておく
　(C)F8を押して実行してしばらくまっていると、DHCPでGR-ROSEに割り当てられたIPアドレス情報がRenesas Debug Virtual Consoleに出力される
　(D)PCからpingをGR-ROSEのIPアドレスに対して送り応答があることを確認
　(E)PCからteratermでGR-ROSEのIPアドレスとTCPポート番号1024に対し接続し、何かキーボードをたたいてエンターキーをたたくとエコーバックがあることを確認




　　　　

　　　
　　　　　　


