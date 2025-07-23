# RemoveOccludedTriangles

`RemoveOccludedTriangles` クラスは、メッシュの「内側」にある、または他のジオメトリによって完全に隠されている（オクルードされている）三角形を削除する機能を提供します。

これは、3Dスキャンデータから内部のノイズを除去したり、複雑なアセンブリモデルから外部から見えないパーツを取り除いたりするのに役立ちます。

## 機能

-   **内側判定モード (`InsideMode`):** 三角形が「内側」にあるかどうかを判断するために、複数のアルゴリズムを選択できます。
    -   `RayParity`: ある点から無限遠にレイを飛ばし、メッシュとの交差回数が奇数回であれば内側と判定します（メッシュが閉じている必要があります）。
    -   `AnalyticWindingNumber`: 厳密な巻き数法（Winding Number）を計算します。値がしきい値（`WindingIsoValue`）より大きい場合、内側と判定します。正確ですが計算コストが高いです。
    -   `FastWindingNumber`: 高速な近似巻き数法を使用します。`AnalyticWindingNumber` よりも高速ですが、精度は若干劣ります。
    -   `SimpleOcclusionTest`: ある点から複数の定義済み方向（X,Y,Zの±方向）にレイをキャストし、すべてのレイがメッシュにヒットした場合に内側（オクルードされている）と判定します。最も単純なオクルージョンテストです。

-   **判定単位 (`PerVertex`):**
    -   `false`（デフォルト）: 各三角形の重心が内側にあるかどうかで判断します。
    -   `true`: 各頂点が内側にあるかどうかを判断し、いずれかの頂点が内側にあれば、その頂点を含む三角形を削除対象とします。

-   **法線オフセット (`NormalOffset`):** 判定点を法線方向にわずかにオフセットすることで、サーフェス上に正確に乗っている場合の数値的な問題を回避します。

## 使い方

```csharp
// 処理したいメッシュ
DMesh3 mesh = ...;
// 高速化のためにAABBTreeを事前に計算しておく（任意）
DMeshAABBTree3 spatial = new DMeshAABBTree3(mesh, true);

// RemoveOccludedTriangles のインスタンスを作成
RemoveOccludedTriangles remover = new RemoveOccludedTriangles(mesh, spatial);

// パラメータを設定
remover.InsideMode = RemoveOccludedTriangles.CalculationMode.FastWindingNumber;
remover.PerVertex = true; // 頂点単位で判定
remover.WindingIsoValue = 0.8; // 巻き数のしきい値

// 処理を実行
bool success = remover.Apply();

if (success) {
    // 処理が完了しました
    // メッシュは直接更新されています

    if (remover.RemovedT != null && remover.RemovedT.Count > 0) {
        Console.WriteLine($"Removed {remover.RemovedT.Count} occluded triangles.");
    }
}
```

## 注意点

-   `RayParity` モードは、メッシュが閉じている（穴がない）場合にのみ正しく機能します。穴がある場合、内部で一時的に穴埋め処理が行われます。
-   巻き数法（`AnalyticWindingNumber`, `FastWindingNumber`）は、メッシュが閉じている場合に最も信頼性が高くなります。
-   どの `InsideMode` を選択するかは、メッシュの特性（閉じているか、自己交差があるかなど）と、要求される精度・速度によって決まります。`FastWindingNumber` が多くの場合で良いバランスを提供します。
-   このクラスはメッシュを直接変更するため、必要であれば事前にコピーを作成してください。
