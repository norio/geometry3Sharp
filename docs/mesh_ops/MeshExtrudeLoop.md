# MeshExtrudeLoop

`MeshExtrudeLoop` クラスは、メッシュの境界ループを押し出す機能を提供します。

## 機能

1. **ループの複製:** 入力された `EdgeLoop` の頂点を複製して、新しいループを作成します。
2. **頂点の移動:** 新しいループの頂点を、`PositionF` 関数に基づいて新しい位置に移動させます。
3. **側面の生成:** 元のループと新しいループを、三角形のリングでつなぎ合わせ（スティッチし）て側面を生成します。

`PositionF` をカスタマイズすることで、押し出し方向や距離、形状を柔軟に制御できます。

## 使い方

```csharp
// メッシュと押し出したい境界ループを取得
DMesh3 mesh = ...;
EdgeLoop boundaryLoop = ...; // これはメッシュの境界ループである必要があります

// MeshExtrudeLoop のインスタンスを作成
MeshExtrudeLoop extrudeOp = new MeshExtrudeLoop(mesh, boundaryLoop);

// 押し出し関数を定義
// (元の頂点位置、法線、ループ内のインデックス) を受け取り、新しい位置を返す
extrudeOp.PositionF = (pos, normal, index) => {
    // 法線方向に 3.0 単位押し出し、少し広げる
    Vector3d newPos = pos + (Vector3d)normal * 3.0;
    newPos += (newPos - boundaryLoop.GetCentroid()).Normalized * 0.5;
    return newPos;
};

// 押し出し処理を実行
// オプションで新しい三角形にグループIDを割り当て可能
bool success = extrudeOp.Extrude(group_id: 1);

if (success) {
    // 押し出しが成功しました
    // メッシュは直接更新されています

    // 押し出しによって生成された情報を取得 (オプション)
    int[] newTriangles = extrudeOp.NewTriangles;
    EdgeLoop newLoop = extrudeOp.NewLoop;
    // ...
}
```

## 事前条件

- 入力する `EdgeLoop` は、メッシュの有効な境界ループである必要があります。`Validate()` メソッドで確認できます。
