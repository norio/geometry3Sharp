# LaplacianMeshSmoother

`LaplacianMeshSmoother` クラスは、ラプラシアンメッシュ平滑化を実装します。メッシュの形状を滑らかにすることを目的としており、`LaplacianMeshDeformer` と似ていますが、変形ではなく平滑化に特化しています。

## 機能

- ラプラシアン行列を使用してメッシュの平滑化を行います。
- `SetConstraint` メソッドを使用して、頂点にソフト制約またはハード制約（PostFix）を設定し、平滑化プロセス中に特定の位置に固定または近づけることができます。
- `Solve` メソッドは、平滑化されたメッシュの頂点位置を `Vector3d` の配列として返します。
- `SolveAndUpdateMesh` メソッドは、メッシュを直接更新します。
- `RegionSmooth` 静的メソッドを使用して、メッシュの特定の領域のみを平滑化することができます。

## 使い方

### メッシュ全体の平滑化

```csharp
// メッシュを取得
DMesh3 mesh = ...;

// LaplacianMeshSmoother のインスタンスを作成
LaplacianMeshSmoother smoother = new LaplacianMeshSmoother(mesh);

// 平滑化から保護したい頂点に制約を設定
int vertexID = 10;
Vector3d position = mesh.GetVertex(vertexID);
double weight = 100.0;
smoother.SetConstraint(vertexID, position, weight);

// 平滑化を実行
bool success = smoother.SolveAndUpdateMesh();
```

### 特定領域の平滑化

```csharp
// メッシュと平滑化したい三角形のリストを取得
DMesh3 mesh = ...;
IEnumerable<int> triangles = ...;

// 領域を平滑化
LaplacianMeshSmoother.RegionSmooth(
    mesh,
    triangles,
    nConstrainLoops: 2,         // 境界から2リング分の頂点を制約
    nIncludeExteriorRings: 1,   // 処理範囲を1リング分拡張
    bPreserveExteriorRings: true, // 拡張したリングの形状を維持
    borderWeight: 10.0,         // 境界の制約の重み
    interiorWeight: 0.1         // 内部の制約の重み
);
```
