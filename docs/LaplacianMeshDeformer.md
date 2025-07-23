# LaplacianMeshDeformer

`LaplacianMeshDeformer` クラスは、ラプラシアンメッシュ変形を実装します。メッシュの形状を滑らかに保ちながら、頂点の位置を制約に基づいて変形させることができます。

## 機能

- ラプラシアン行列を使用してメッシュの形状を表現します。
- `SetConstraint` メソッドを使用して、頂点にソフト制約またはハード制約（PostFix）を設定できます。
- `Solve` メソッドは、制約を満たすようにメッシュを変形し、結果を `Vector3d` の配列として返します。
- `SolveAndUpdateMesh` メソッドは、メッシュを直接更新します。
- `UseSoftConstraintNormalEquations` プロパティを使用して、計算方法を切り替えることができます。

## 使い方

```csharp
// メッシュを取得
DMesh3 mesh = ...;

// LaplacianMeshDeformer のインスタンスを作成
LaplacianMeshDeformer deformer = new LaplacianMeshDeformer(mesh);

// 変形させたい頂点に制約を設定
int vertexID = 10;
Vector3d targetPosition = new Vector3d(1, 2, 3);
double weight = 100.0;
deformer.SetConstraint(vertexID, targetPosition, weight);

// 別の頂点を固定
int fixedVertexID = 20;
Vector3d fixedPosition = mesh.GetVertex(fixedVertexID);
deformer.SetConstraint(fixedVertexID, fixedPosition, 1000.0, true);

// 変形を実行
Vector3d[] deformedVertices = new Vector3d[mesh.MaxVertexID];
bool success = deformer.Solve(deformedVertices);

if (success) {
    // 変形後の頂点位置を使用してメッシュを更新
    for (int i = 0; i < mesh.MaxVertexID; ++i) {
        if (mesh.IsVertex(i)) {
            mesh.SetVertex(i, deformedVertices[i]);
        }
    }
}

// または、直接メッシュを更新
// bool success = deformer.SolveAndUpdateMesh();
```
