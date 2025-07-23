# MeshTopology

`MeshTopology` クラスは、メッシュの「トポロジカルな骨格」を抽出します。これは、メッシュの形状を定義する重要な特徴、つまり境界エッジと鋭い折り目（クリースエッジ）を特定し、それらを連続したパス（スパンとループ）に整理することによって行われます。

## 機能

1.  **特徴エッジの検出:**
    -   **`BoundaryEdges`**: メッシュの境界にあるエッジをすべて検出します。
    -   **`CreaseEdges`**: 隣接する2つの三角形の法線間の角度が `CreaseAngle` を超えるエッジを、クリースエッジとして検出します。

2.  **トポロジの抽出:**
    -   検出されたすべての特徴エッジ（`AllEdges`）からグラフ（`DGraph3`）を構築します。
    -   `DGraph3Util.ExtractCurves` を使用して、このグラフを連続したパスに分解します。
    -   分解されたパスを、メッシュの `EdgeSpan`（開いたパス）と `EdgeLoop`（閉じたループ）に変換します。
    -   3つ以上のパスが接続する頂点を `JunctionVertices` として特定します。

3.  **再メッシュ化のための制約生成:**
    -   `AddRemeshConstraints` メソッドは、抽出されたトポロジ（スパン、ループ、ジャンクション）を `MeshConstraints` オブジェクトに追加します。これにより、再メッシュ化（Remeshing）の際に、メッシュの重要な特徴が保持されるようになります。

4.  **結果のキャッシュ:**
    -   計算されたトポロジは、`Mesh.ShapeTimestamp` に基づいてキャッシュされます。メッシュが変更されない限り、再計算は行われません。

## 使い方

```csharp
// トポロジを抽出したいメッシュ
DMesh3 mesh = ...;

// MeshTopology のインスタンスを作成
MeshTopology topology = new MeshTopology(mesh);

// (オプション) クリースとして検出する角度のしきい値を設定
topology.CreaseAngle = 45.0; // デフォルトは 30.0

// トポロジを計算
topology.Compute();

// 抽出された要素にアクセス
EdgeLoop[] loops = topology.Loops;
EdgeSpan[] spans = topology.Spans;
HashSet<int> junctions = topology.JunctionVertices;

// 例えば、抽出されたループを処理
foreach (EdgeLoop loop in loops) {
    // ...
}


// --- 再メッシュ化での利用例 ---

// Remesher をセットアップ
Remesher r = new Remesher(mesh);

// トポロジを制約として追加
topology.AddRemeshConstraints(r.Constraints);

// 再メッシュ化を実行
r.BasicRemeshPass();
```

## `MakeElementsMesh`

このクラスには、抽出したトポロジを視覚化するための `MakeElementsMesh` というデバッグ用のヘルパーメソッドも含まれています。これは、各スパンとループに沿ってチューブメッシュを生成します。
