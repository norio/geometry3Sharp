# MeshAssembly

`MeshAssembly` クラスは、入力されたメッシュを意味のあるコンポーネント（閉じたソリッド、開いたメッシュなど）に分解しようと試みます。

## 機能

- 接続されたコンポーネントを分離します。
- `RemoveDuplicateTriangles` や `MergeCoincidentEdges` を使用して、簡単なメッシュ修復を試みます。
- `HasNoVoids` プロパティが `true` の場合、各シェルを個別のソリッドとして扱います。
- 分解の結果は、`ClosedSolids` と `OpenMeshes` のリストに格納されます。

## 使い方

```csharp
// ソースメッシュを取得
DMesh3 sourceMesh = ...;

// MeshAssembly のインスタンスを作成
MeshAssembly assembly = new MeshAssembly(sourceMesh);

// ボイドがないと仮定する場合 (オプション)
// assembly.HasNoVoids = true;

// 分解処理を実行
assembly.Decompose();

// 結果を取得
List<DMesh3> closedSolids = assembly.ClosedSolids;
List<DMesh3> openMeshes = assembly.OpenMeshes;

// 分解されたメッシュを処理
foreach (DMesh3 solid in closedSolids) {
    // ...
}
foreach (DMesh3 openMesh in openMeshes) {
    // ...
}
```
