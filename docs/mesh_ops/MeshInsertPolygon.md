# MeshInsertPolygon

`MeshInsertPolygon` クラスは、3DメッシュのXY平面上に2Dポリゴン（穴を持つ場合も含む）を挿入（刻印）する機能を提供します。

このクラスは内部で `MeshInsertUVPolyCurve` を使用して、ポリゴンの外側と内側の穴の境界線をメッシュにエッジとして挿入し、その後、ポリゴンの内側にある三角形を特定します。

## 機能

- **ポリゴンの挿入:** `GeneralPolygon2d` で定義されたポリゴン（外側ループとオプションの穴ループ）をメッシュに挿入します。
- **単純化:** `SimplifyInsertion` が `true` の場合、挿入されたエッジを単純化して不要な頂点を削減します。
- **内部領域の特定:** 挿入されたポリゴンの内側にある三角形を `MeshFaceSelection` として特定します。
- **結果へのアクセス:** 挿入されたエッジ（`InsertedPolygonEdges`）と内部の三角形（`InteriorTriangles`）にアクセスできます。

## 使い方

```csharp
// XY平面上にジオメトリを持つメッシュと、挿入したい2Dポリゴンを取得
DMesh3 mesh = ...; // このメッシュの頂点は (x, y, 0) の形式であると想定
GeneralPolygon2d polygon = new GeneralPolygon2d(...);

// MeshInsertPolygon のインスタンスを作成
MeshInsertPolygon inserter = new MeshInsertPolygon();
inserter.Mesh = mesh;
inserter.Polygon = polygon;

// 挿入エッジの単純化を無効にする場合 (オプション)
// inserter.SimplifyInsertion = false;

// ポリゴンを挿入
bool success = inserter.Insert();

if (success) {
    // 挿入が成功しました

    // 挿入されたエッジのセットを取得
    HashSet<int> insertedEdges = inserter.InsertedPolygonEdges;

    // ポリゴン内部の三角形のセレクションを取得
    MeshFaceSelection interiorFaces = inserter.InteriorTriangles;

    // 内部の三角形を削除するなどの操作が可能
    MeshEditor editor = new MeshEditor(mesh);
    editor.RemoveTriangles(interiorFaces, true);
}
```

## 事前条件

- このクラスは、入力 `Mesh` の頂点座標がXY平面上にある（Z座標が0である）ことを前提としています。3D曲面上にポリゴンを投影して挿入する場合は、`MeshInsertProjectedPolygon` を使用してください。
