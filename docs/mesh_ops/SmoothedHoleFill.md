# SmoothedHoleFill

`SmoothedHoleFill` クラスは、メッシュの穴を高品質で滑らかなサーフェスで埋めるための、包括的なワークフローを提供します。

このクラスは、単純な穴埋め、再メッシュ化、ラプラシアン平滑化といった複数の操作を組み合わせて、周囲のジオメトリと自然に調和するフィルを生成します。

## 機能

1.  **初期フィル:** `SimpleHoleFiller` を使用して、穴を一時的に塞ぎます。
2.  **オフセット（オプション）:** `OffsetDirection` と `OffsetDistance` が設定されている場合、初期フィル面を指定された方向に押し出します。
3.  **第1再メッシュ化（オプション）:** `RemeshBeforeSmooth` が `true` の場合、フィル領域を `RegionRemesher` で再メッシュ化し、トポロジを整えます。
4.  **ラプラシアン平滑化:** `EnableLaplacianSmooth` が `true` の場合、`LaplacianMeshSmoother.RegionSmooth` を使用して、フィル領域とその周辺を滑らかにします。`SmoothSolveIterations` で反復回数を制御できます。
5.  **第2再メッシュ化（オプション）:** `RemeshAfterSmooth` が `true` の場合、平滑化された領域を再度再メッシュ化し、最終的なサーフェスの品質を向上させます。この際、元のメッシュ形状にサーフェスを射影して、形状の崩壊を防ぎます。
6.  **領域の制約 (`ConstrainToHoleInterior`):**
    -   `true`: すべての操作を、最初にフィルされた三角形の領域内に限定します。元のメッシュの境界部分は変更されませんが、フィル品質が低下することがあります。
    -   `false`（デフォルト）: 操作領域を穴の周囲に数リング分拡大します。これにより、フィルと既存メッシュの境界がより滑らかになりますが、元のメッシュも変更されます。

## 使い方

```csharp
// 穴を埋めたいメッシュと、その境界ループを取得
DMesh3 mesh = ...;
EdgeLoop boundaryLoop = ...;

// SmoothedHoleFill のインスタンスを作成
SmoothedHoleFill filler = new SmoothedHoleFill(mesh, boundaryLoop);

// パラメータを設定
filler.TargetEdgeLength = 5.0;      // 目標エッジ長
filler.SmoothAlpha = 1.0;           // 平滑化の強さ
filler.InitialRemeshPasses = 10;    // 初期再メッシュ化のパス回数
filler.SmoothSolveIterations = 3;   // ラプラシアン平滑化の反復回数

// (オプション) フィル領域を元のメッシュの境界内に限定する
// filler.ConstrainToHoleInterior = true;

// 穴埋め処理を実行
bool success = filler.Apply();

if (success) {
    // 穴埋めが成功しました
    // メッシュは直接更新されています

    // (オプション) フィルによって生成された三角形や頂点を取得
    int[] filledTriangles = filler.FillTriangles;
    int[] filledVertices = filler.FillVertices;
}
```

## 注意点

-   このクラスは多くの操作を内部で実行するため、比較的高度な設定が可能です。`ConfigureRemesherF` デリゲートを使用すると、内部の `Remesher` の動作をさらに細かくカスタマイズできます。
-   `FillLoop` を指定しない場合、クラスはメッシュ内の最大の境界ループを自動的に選択しようとします。`BorderHintTris` を使用して、特定の穴をターゲットにすることも可能です。
-   高品質な結果が得られる一方で、他の穴埋めクラスよりも計算コストが高くなります。
