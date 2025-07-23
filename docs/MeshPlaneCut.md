# MeshPlaneCut

`MeshPlaneCut` クラスは、メッシュを指定された平面で切断する機能を提供します。

## 機能

1. **頂点の分類:** メッシュの各頂点が、平面の正の側、負の側、または平面上のいずれにあるかを判断します。
2. **交差エッジの分割:** 平面と交差するすべてのメッシュエッジを、交点で `SplitEdge` を使って分割します。
3. **ジオメトリの削除:** 平面の正の側（法線が指す側）にあるすべての頂点を削除します。これにより、その頂点に接続されていた三角形も削除されます。
4. **縮退エッジの折りたたみ:** `CollapseDegenerateEdgesOnCut` が `true` の場合、切断によって生成された非常に短い（縮退した）境界エッジを折りたたみ、クリーンなカットラインを生成します。
5. **カットループの抽出:** 切断によって新しく生成された境界エッジを追跡し、それらからなる `EdgeLoop`（閉じたループ）または `EdgeSpan`（開いたパス）を抽出します。
6. **穴埋め:** `FillHoles()` メソッドを呼び出すことで、抽出された `CutLoops` を `SimpleHoleFiller` を使って単純な三角形で埋めることができます。

## 使い方

```csharp
// カットしたいメッシュと、カットに使用する平面を定義
DMesh3 mesh = ...;
Vector3d planeOrigin = new Vector3d(0, 10, 0); // 平面上の点
Vector3d planeNormal = Vector3d.AxisY;       // 平面の法線

// MeshPlaneCut のインスタンスを作成
MeshPlaneCut cutter = new MeshPlaneCut(mesh, planeOrigin, planeNormal);

// (オプション) カットラインの縮退エッジを折りたたむ
cutter.CollapseDegenerateEdgesOnCut = true;
cutter.DegenerateEdgeTol = 1e-6;

// カット処理を実行
bool success = cutter.Cut();

if (success) {
    // カットが成功しました
    // メッシュは直接更新されています

    // カットによって生成された境界ループを取得
    List<EdgeLoop> cutLoops = cutter.CutLoops;
    foreach (EdgeLoop loop in cutLoops) {
        // 各ループに対する処理
    }

    // (オプション) カットによってできた穴を埋める
    if (cutter.CutLoopsFailed == false) {
        cutter.FillHoles();
    }
}
```

## 注意点

- `Cut()` メソッドは、平面の法線が指す側（正の側）のジオメトリを削除します。反対側を削除したい場合は、法線を反転させてください。
- カット操作によってメッシュのトポロジが複雑に変化した場合（例えば、カットが既存の穴と交差した場合）、`CutLoopsFailed` が `true` になり、`CutLoops` の抽出に失敗することがあります。
- `FillHoles()` は単純な穴埋めです。より高度な穴埋めが必要な場合は、`CutLoops` を取得し、`PlanarHoleFiller` や `SmoothedHoleFill` などの他のクラスを自分で使用する必要があります。
