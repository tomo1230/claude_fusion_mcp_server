# Fusion MCP Server for Claude Desktop

**バージョン: 0.8.8 (Beta) ファイル連携バージョン**

このプロジェクトは、**Claude Desktop**がAutodesk Fusion を直接操作するためのModel Context Protocol (MCP)サーバーです。このツールをClaude Desktopに追加することで、チャットのプロンプトを通じて3Dモデルの作成、編集、情報取得が可能になります。

このサーバーは、Fusion 内で動作する[対応するPythonアドイン](<リンク_to_Pythonアドインのリポジトリ>)と連携して機能します。

- **作者:** Kanbara Tomonori
- **X (旧Twitter):** [@tomo1230](https://x.com/tomo1230)
- **ライセンス:** 本ソースコードはプロプライエタリかつ機密情報です。無断での複製、修正、配布、使用は固く禁じられています。

---

## 概要とアーキテクチャ

このツールは、Claudeとの対話を通じて、直感的かつ自然言語ベースでFusion のモデリング作業を行うためのブリッジとして機能します。

**処理フロー:**
1.  ユーザーがClaude Desktopで `@Fusion360` のようなツール名を指定してプロンプトを送信します。（例: `@Fusion360 50mmの立方体を作って`）
2.  Claude Desktopは、このNode.jsサーバーを子プロセスとして起動し、`CallToolRequest` を送信します。
3.  Node.jsサーバーはリクエストをJSONコマンドに変換し、`~/Documents/fusion_command.txt` に書き込みます。
4.  Fusion 内で起動しているPythonアドインがこのファイルを検知し、Fusion のAPIを実行します。
5.  Pythonアドインは実行結果を `~/Documents/fusion_response.txt` に書き込みます。
6.  Node.jsサーバーがレスポンスを読み取り、Claude Desktopに結果を返します。
7.  Claudeがその結果を解釈し、ユーザーに応答します。



---

## セットアップガイド for Claude Desktop

### Step 1: 前提条件の確認
-   **Claude Desktop**: アプリケーションがインストールされていること。
-   **Node.js**: v18以降がインストールされていること。
-   **Autodesk Fusion **: 最新版がインストールされていること。
-   **Fusion  Pythonアドイン**: **これが最も重要です。**[対応するPythonアドイン](<リンク_to_Pythonアドインのリポジトリ>)がFusion にインストールされ、ツールバーの**「連携開始」**ボタンが押されている状態にしてください。

### Step 2: MCPサーバーのインストール
1.  任意の場所にこのリポジトリをクローン（またはダウンロード）します。
    ```bash
    git clone <repository_url>
    ```
2.  ターミナルでそのディレクトリに移動し、依存関係をインストールします。
    ```bash
    cd <repository_directory>
    npm install
    ```

### Step 3: Claude Desktopへのツール追加
1.  Claude Desktopを開き、ツールの設定画面に移動します。
2.  「ツールを追加」や「MCPサーバーを追加」のようなボタンをクリックします。
3.  先ほどクローンした**リポジトリのフォルダ**を選択して追加します。Claude Desktopが自動的に `fusion_mcp_server.js` を認識します。

### Step 4: Claude Desktopでの使用
セットアップが完了すれば、チャットでFusion を操作できます。

**使用例:**
-   `@Fusion360 幅50、奥行き30、高さ20の箱を作って`
-   `@Fusion360 "MyCube" という名前の立方体を作成して、その寸法を教えて`
-   `@Fusion360 最後に作ったボディに半径2mmのフィレットを追加して`

---

## APIリファレンス / 利用可能なツール

Claudeは以下のツールを呼び出すことでFusion を操作します。

### 形状作成ツール
-   **`create_cube`**: 立方体を作成
-   **`create_cylinder`**: 円柱を作成
-   **`create_box`**: 直方体を作成
-   **`create_sphere`**: 球を作成
-   **`create_hemisphere`**: 半球を作成
-   **`create_cone`**: 円錐を作成
-   **`create_polygon_prism`**: 多角柱を作成
-   **`create_torus`**: トーラスを作成
-   **`create_half_torus`**: 半分のトーラスを作成
-   **`create_pipe`**: 2点間にパイプを作成
-   **`create_polygon_sweep`**: ねじれたリング形状を作成

### 編集・変形ツール
-   **`add_fillet`**: フィレット（角丸め）を追加
-   **`add_chamfer`**: 面取りを追加
-   **`combine_by_name`**: ブーリアン演算（結合、切り取り、交差）を実行
-   **`move_by_name`**: ボディを移動
-   **`rotate_by_name`**: ボディを回転

### パターン・コピー
-   **`copy_body_symmetric`**: 対称コピー（ミラー）
-   **`create_circular_pattern`**: 円形状に複製
-   **`create_rectangular_pattern`**: 矩形状に複製

### 情報取得ツール
-   **`get_bounding_box`**: バウンディングボックスを取得
-   **`get_body_center`**: ボディの中心を取得
-   **`get_body_dimensions`**: ボディの寸法（体積、表面積など）を取得
-   **`get_faces_info`**: 全ての面の情報を取得
-   **`get_edges_info`**: 全てのエッジの情報を取得
-   **`get_mass_properties`**: 質量特性を計算
-   **`get_body_relationships`**: 2ボディ間の関係（距離、干渉など）を取得
-   **`measure_distance`**: 2ボディ間の最短距離を測定

### ユーティリティ
-   **`execute_macro`**: 複数のコマンドを連続実行
-   **`select_body` / `select_all_bodies`**: ボディを選択
-   **`hide_body` / `show_body`**: ボディの表示/非表示
-   **`debug_coordinate_info`**: 座標系のデバッグ情報を取得
