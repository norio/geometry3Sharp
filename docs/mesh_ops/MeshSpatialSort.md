# MeshSpatialSort

`MeshSpatialSort` クラスは、複数のメッシュコンポーネントを受け取り、それらの間の空間的な包含関係（どのメッシュがどのメッシュの内側にあるか）を解決します。

結果は `MeshSolid` のリストとして整理されます。各 `MeshSolid` は、1つの「外側」のメッシュ（`Outer`）と、その内部に含まれる複数の「キャビティ」メッシュ（`Cavities`）のリストで構成されます。

## 機能

- **コンポーネントの追加:** `AddMesh` メソッドを使用して、ソート対象のメッシュを `ComponentMesh` として追加します。
- **包含関係の判定:** `ComponentMesh.Contains` メソッドは、高速な巻き数法（Fast Winding Number）を利用して、あるメッシュが別のメッシュの内部に完全に含まれているかを効率的に判定します。
- **ソート処理:** `Sort()` メソッドがメインの処理です。
    1.  すべてのメッシュペア間で包含関係をテストします。
    2.  バウンディングボックスによる事前チェックで、明らかに包含関係にないペアを高速に除外します。
    3.  包含関係のグラフを構築します。
    4.  このグラフを解析し、どのメッシュが「外側」で、どれがその「キャビティ」であるかを特定し、`MeshSolid` オブジェクトに整理します。

## 使い方

```csharp
// ソートしたいメッシュのリストを用意
List<DMesh3> meshes = ...;

// MeshSpatialSort のインスタンスを作成
MeshSpatialSort sorter = new MeshSpatialSort();

// 各メッシュをコンポーネントとして追加
for (int i = 0; i < meshes.Count; ++i) {
    // 識別子としてインデックスや名前などを渡せる
    sorter.AddMesh(meshes[i], i);
}

// (オプション) 開いたメッシュをコンテナとして許可する
// sorter.AllowOpenContainers = true;

// ソート処理を実行
sorter.Sort();

// 結果の MeshSolid のリストを取得
List<MeshSpatialSort.MeshSolid> solids = sorter.Solids;

// 結果を処理
foreach (MeshSpatialSort.MeshSolid solid in solids) {
    // 外側のメッシュ
    DMesh3 outerMesh = solid.Outer.Mesh;
    object outerIdentifier = solid.Outer.Identifier;
    Console.WriteLine($"Solid Outer: {outerIdentifier}");

    // 内側のキャビティ（穴）メッシュ
    foreach (MeshSpatialSort.ComponentMesh cavity in solid.Cavities) {
        DMesh3 cavityMesh = cavity.Mesh;
        object cavityIdentifier = cavity.Identifier;
        Console.WriteLine($"  - Cavity: {cavityIdentifier}");
    }
}
```

## 注意点

- 包含関係の判定は、`DMeshAABBTree3` と高速巻き数法に依存するため、閉じたメッシュに対して最も正確に機能します。`AllowOpenContainers = true` に設定すると、開いたメッシュもコンテナとして扱われますが、結果の信頼性は低下する可能性があります。
- ソート処理は計算コストが高い可能性があり、特にメッシュの数が多い場合に時間がかかることがあります。処理は `gParallel.ForEach` を利用して並列化されています。
