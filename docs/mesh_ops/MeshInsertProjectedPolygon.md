# MeshInsertProjectedPolygon

`MeshInsertProjectedPolygon` クラスは、指定された平面（`Frame3f`）に2Dポリゴンを投影し、それを3Dメッシュに挿入（刻印）する機能を提供します。

## 機能

1. **ROIの特定:** `SeedTriangle` とポリゴンに基づいて、挿入操作の影響を受けるメッシュの関心領域（ROI）を特定します。
2. **サブメッシュの作成:** ROIからサブメッシュを作成し、`RegionOperator` を使用して管理します。
3. **平面への投影:** サブメッシュを `ProjectFrame` を使ってXY平面に投影します。
4. **ポリゴンの挿入:** 平面化されたサブメッシュに `MeshInsertUVPolyCurve` を使ってポリゴンを挿入します。
5. **単純化:** `SimplifyInsertion` が `true` の場合、挿入されたエッジを単純化します。
6. **内部の削除:** `RemovePolygonInterior` が `true` の場合、ポリゴン内部の三角形を削除します。
7. **3Dへの逆投影:** 変更されたサブメッシュを、重心座標補間を用いて元の3D空間に戻します。
8. **メッシュの更新:** `RegionOperator` を使って、変更を元のメッシュに反映（Back-propagate）します。

## 使い方

```csharp
// ターゲットメッシュ、挿入するポリゴン、投影用のフレーム、シード三角形を取得
DMesh3 mesh = ...;
Polygon2d polygon = ...;
Frame3f projectionFrame = ...; // ポリゴンを投影する平面
int seedTriangle = ...; // ポリゴンと交差するメッシュ上の三角形ID

// MeshInsertProjectedPolygon のインスタンスを作成
MeshInsertProjectedPolygon inserter = new MeshInsertProjectedPolygon(
    mesh, polygon, projectionFrame, seedTriangle);

// オプションを設定
inserter.SimplifyInsertion = true;    // 挿入エッジを単純化
inserter.RemovePolygonInterior = true; // ポリゴン内部を削除して穴を開ける

// 挿入処理を実行
bool success = inserter.Insert();

if (success) {
    // 挿入が成功しました
    // メッシュは直接更新されています

    // 結果を取得 (オプション)
    EdgeLoop insertedLoop = inserter.InsertedLoop; // 挿入された境界ループ
    int[] insertedVerts = inserter.InsertedPolygonVerts; // 挿入されたポリゴンの頂点ID
    RegionOperator modifiedRegion = inserter.ModifiedRegion; // 操作に使用されたRegionOperator
    // ...
}
```

## 事前条件

- `SeedTriangle` は、投影されたポリゴンが交差するメッシュ上の有効な三角形でなければなりません。これにより、操作範囲を効率的に特定できます。
- `ProjectFrame` のZ軸が、投影面の法線となるように設定する必要があります。
