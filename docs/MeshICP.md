# MeshICP

`MeshICP` クラスは、Iterative Closest Point (ICP) アルゴリズムを実装します。`Source` ポイントセットを `TargetSurface` メッシュに位置合わせするための最適な剛体変換（回転と並進）を見つけます。

## 機能

- **剛体変換の計算:** `Source` を `TargetSurface` に最もよく一致させるための `Rotation` (Quaterniond) と `Translation` (Vector3d) を計算します。
- **反復計算:** `MaxIterations` で指定された最大反復回数まで、または `ConvergeTolerance` で指定された収束許容値に達するまで計算を繰り返します。
- **法線の利用:** `UseNormals` を `true` に設定すると、対応点のペアリングにおいて法線情報も考慮し、より精度の高い位置合わせを目指します。
- **距離制限:** `MaxAllowableDistance` を設定することで、一定距離以上離れた対応点を無視することができます。
- **詳細な出力:** `VerboseF` デリゲートを設定することで、計算の進行状況をログに出力できます。

## 使い方

```csharp
// ソースのポイントセットとターゲットのメッシュ（AABBTree付き）を取得
IPointSet sourcePoints = ...;
DMesh3 targetMesh = ...;
DMeshAABBTree3 targetTree = new DMeshAABBTree3(targetMesh, true);

// MeshICP のインスタンスを作成
MeshICP icp = new MeshICP(sourcePoints, targetTree);

// パラメータを設定 (オプション)
icp.MaxIterations = 100;
icp.UseNormals = true;
icp.ConvergeTolerance = 1e-6;
icp.VerboseF = (message) => { Console.WriteLine(message); };

// ICPアルゴリズムを実行
icp.Solve();

if (icp.Converged) {
    // 収束した場合

    // 計算された変換を取得
    Quaterniond rotation = icp.Rotation;
    Vector3d translation = icp.Translation;

    // この変換をソースオブジェクトに適用する
    // (例: ソースが変形可能なメッシュの場合)
    if (sourcePoints is IDeformableMesh sourceMesh) {
        icp.UpdateVertices(sourceMesh);
    }
}
```

## `IPointSet` と `IDeformableMesh`

- `Source` は `IPointSet` インターフェースを実装する任意のオブジェクト（`DMesh3` など）です。
- `UpdateVertices` メソッドを使用してソースの頂点位置を直接更新したい場合は、ソースオブジェクトが `IDeformableMesh` インターフェースを実装している必要があります。
