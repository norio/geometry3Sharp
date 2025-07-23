# MeshExtrudeMesh

`MeshExtrudeMesh` クラスは、メッシュ全体を押し出して厚みをつける（ソリッド化する）機能を提供します。

## 機能

1. **メッシュの複製:** 入力メッシュのすべての三角形を複製し、オフセットサーフェスを作成します。
2. **頂点の移動:** 複製されたオフセットサーフェスの頂点を、`ExtrudedPositionF` 関数に基づいて新しい位置に移動させます。
3. **法線の反転:** 元のサーフェスまたはオフセットサーフェスのどちらかの三角形の向きを反転させます（`IsPositiveOffset` に基づく）。これにより、押し出し後のメッシュが閉じたソリッドになるようにします。
4. **側面の生成:** 元のメッシュの各境界ループと、それに対応するオフセットサーフェスの新しい境界ループを、三角形のストリップでつなぎ合わせ（スティッチし）て側面を生成します。

## 使い方

```csharp
// 押し出したいメッシュを取得
DMesh3 mesh = ...; // 開いたメッシュ、または閉じたメッシュ

// MeshExtrudeMesh のインスタンスを作成
MeshExtrudeMesh extrudeOp = new MeshExtrudeMesh(mesh);

// 押し出し関数を定義
// (元の頂点位置、法線、頂点ID) を受け取り、新しい位置を返す
extrudeOp.ExtrudedPositionF = (pos, normal, vid) => {
    // 法線方向に 2.0 単位押し出す
    return pos + (Vector3d)normal * 2.0;
};

// 押し出し方向が内向きの場合は false に設定
// extrudeOp.IsPositiveOffset = false;

// 押し出し処理を実行
bool success = extrudeOp.Extrude();

if (success) {
    // 押し出しが成功しました
    // メッシュは直接更新され、厚みのあるソリッドになっています

    // 押し出しによって生成された情報を取得 (オプション)
    int[] offsetTriangles = extrudeOp.OffsetTriangles.ToArray();
    int[][] stitchTriangles = extrudeOp.StitchTriangles;
    // ...
}
```

## 注意点

- この操作は、入力メッシュが閉じていない（境界ループを持つ）場合に最も効果的です。閉じたメッシュに対して実行すると、2つの重複したサーフェスが生成されるだけで、側面は生成されません。
- `ExtrudedPositionF` で負のオフセット（内側への押し出し）を行う場合は、`IsPositiveOffset` を `false` に設定して、法線の反転が正しく行われるようにする必要があります。
