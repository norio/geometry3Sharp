# MeshExtrudeFaces

`MeshExtrudeFaces` クラスは、メッシュの特定の面（三角形）のセットを押し出す機能を提供します。

## 機能

1. **面の分離:** 押し出し対象の三角形を、隣接する三角形から分離します。
2. **頂点の移動:** 分離された面の頂点を、`ExtrudedPositionF` 関数に基づいて新しい位置に移動させます。
3. **側面の生成:** 元の境界エッジと押し出された新しい境界エッジを、三角形のストリップでつなぎ合わせ（スティッチし）て側面を生成します。

`ExtrudedPositionF` をカスタマイズすることで、押し出し方向や距離を柔軟に制御できます。

## 使い方

```csharp
// メッシュと押し出したい三角形の配列を取得
DMesh3 mesh = ...;
int[] extrudeTris = ...;

// MeshExtrudeFaces のインスタンスを作成
MeshExtrudeFaces extrudeOp = new MeshExtrudeFaces(mesh, extrudeTris);

// 押し出し関数を定義
// (元の頂点位置、法線、頂点ID) を受け取り、新しい位置を返す
extrudeOp.ExtrudedPositionF = (pos, normal, vid) => {
    // 法線方向に 5.0 単位押し出す
    return pos + (Vector3d)normal * 5.0;
};

// 押し出し処理を実行
bool success = extrudeOp.Extrude();

if (success) {
    // 押し出しが成功しました
    // メッシュは直接更新されています

    // 押し出しによって生成された情報を取得 (オプション)
    int[] joinTriangles = extrudeOp.JoinTriangles;
    MeshVertexSelection extrudedVertices = extrudeOp.ExtrudeVertices;
    // ...
}
```

## 注意点

- 複数領域の押し出しは、意図通りに動作しない可能性があります。
- 押し出し操作に失敗した場合（`Extrude` が `false` を返した場合）、メッシュは不正な状態（穴が開いているなど）になっている可能性があるため、注意が必要です。
