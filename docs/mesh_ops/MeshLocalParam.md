# MeshLocalParam

`MeshLocalParam` クラスは、メッシュの局所的な領域に対して、2Dのパラメトリック座標（UV座標）を計算します。これは、メッシュのサーフェスを局所的に平坦化（アンラップ）する操作に相当します。

ダイクストラ法に似たアルゴリズムを使用して、シードとなる三角形（`seedNbrs`）から隣接する頂点へと伝播していき、各頂点のUV座標を計算します。

## 機能

- **UV計算モード:** `UVMode` プロパティで、UV座標の計算方法を選択できます。
    - `ExponentialMap`: 各頂点を、その親頂点の接平面上で展開（Exponential Map）してUVを計算します。高速ですが、歪みが蓄積しやすいです。
    - `ExponentialMap_UpwindAvg`: `ExponentialMap` を改良し、複数の隣接する計算済み頂点からのUVを距離で重み付け平均します。よりロバストで滑らかな結果になります。
    - `PlanarProjection`: すべての頂点を、シードフレーム（`SeedFrame`）に単純に投影します。サーフェスが平坦に近い場合に有効です。
- **距離制限:** `ComputeToMaxDistance` メソッドを使用することで、シードからのグラフ距離が指定した最大値（`fMaxGraphDistance`）に達するまで計算を制限できます。
- **汎用的な入力:** `Func` デリゲート（`PositionF`, `NormalF`, `NeighboursF`）を使用することで、`DMesh3` 以外のグラフ構造に対しても適用可能です。
- **結果へのアクセス:** `GetUV(vertexID)` で各頂点の計算済みUV座標を取得したり、`ApplyUVs(action)` で計算結果をまとめて適用したりできます。

## 使い方

```csharp
// パラメータ計算を行いたいメッシュ
DMesh3 mesh = ...;
// シードとなる三角形の頂点ID
Index3i seedTriangleVerts = ...;
// シード三角形から計算した接平面フレーム
Frame3f seedFrame = ...;

// MeshLocalParam のインスタンスを作成
MeshLocalParam param = new MeshLocalParam(
    mesh.MaxVertexID,
    (vid) => { return (Vector3f)mesh.GetVertex(vid); },
    (vid) => { return (Vector3f)mesh.GetVertexNormal(vid); },
    (vid) => { return mesh.VtxVerticesItr(vid); }
);

// パラメータを設定
param.UVMode = MeshLocalParam.UVModes.ExponentialMap_UpwindAvg;

// 計算を実行（シードからのグラフ距離が 20.0 以下の範囲）
param.ComputeToMaxDistance(seedFrame, seedTriangleVerts, 20.0f);

// 計算されたUV座標を取得して利用
foreach (int vid in mesh.VertexIndices()) {
    Vector2f uv = param.GetUV(vid);
    if (uv != MeshLocalParam.InvalidUV) {
        // uv を使った処理
    }
}

// または、Action を使って一括適用
// mesh.EnableVertexUVs(new Vector2f(0,0));
// param.ApplyUVs((vid, uv) => {
//     mesh.SetVertexUV(vid, uv);
// });
```

## 注意点

- このクラスは、シードから到達可能な連結された領域に対してのみUVを計算します。
- 計算されるUV座標は、シードフレームを基準とした相対的なものです。`TransformUV` を使って、スケールや平行移動を適用できます。
