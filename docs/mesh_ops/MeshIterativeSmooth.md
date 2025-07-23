# MeshIterativeSmooth

`MeshIterativeSmooth` クラスは、指定されたメッシュの頂点セットに対して、反復的な平滑化（スムージング）操作を行います。

## 機能

- **反復平滑化:** 指定された `Rounds` の数だけ、平滑化処理を繰り返します。
- **平滑化タイプの選択:** `SmoothType` プロパティを使用して、以下の平滑化アルゴリズムを選択できます。
    - `Uniform`: 隣接頂点の平均位置に移動させます。最も単純で高速です。
    - `Cotan`: コタンジェントウェイトを使用したラプラシアン平滑化。メッシュの形状をより良く保持します。
    - `MeanValue`: 平均値座標（Mean Value Coordinates）を使用した平滑化。凹形状に強いです。
- **平滑化係数:** `Alpha` プロパティで平滑化の強さを制御します（0は変化なし、1は最大）。
- **投影機能:** `ProjectF` デリゲートを設定することで、各平滑化ステップの後に、計算された新しい頂点位置を別のサーフェスや位置に再投影することができます。これにより、形状を拘束しながら平滑化を行えます。
- **並列処理:** 平滑化計算は `gParallel.ForEach` を使用して並列で実行され、パフォーマンスが向上します。

## 使い方

```csharp
// 平滑化したいメッシュと頂点の配列を取得
DMesh3 mesh = ...;
int[] verticesToSmooth = ...; // 特定の頂点のみを平滑化する場合

// MeshIterativeSmooth のインスタンスを作成
MeshIterativeSmooth smoother = new MeshIterativeSmooth(mesh, verticesToSmooth);

// パラメータを設定
smoother.Rounds = 20; // 20回反復
smoother.Alpha = 0.5; // 平滑化の強さ
smoother.SmoothType = MeshIterativeSmooth.SmoothTypes.Cotan; // コタン平滑化を使用

// (オプション) 投影関数を設定
// 例: 元のメッシュサーフェス上に再投影する
// DMeshAABBTree3 targetTree = new DMeshAABBTree3(originalMesh, true);
// smoother.ProjectF = (pos, normal, vid) => {
//     int tID = targetTree.FindNearestTriangle(pos);
//     return targetTree.Mesh.GetTriBaryPoint(tID, pos);
// };

// 平滑化処理を実行
bool success = smoother.Smooth();

if (success) {
    // 平滑化が成功しました
    // メッシュは直接更新されています
}
```

## 注意点

- `Vertices` 配列には、平滑化したい頂点のIDが含まれている必要があります。メッシュ全体の頂点を平滑化する場合は、`mesh.VertexIndices().ToArray()` を渡します。
- `ProjectF` を使用しない場合、特に境界のあるメッシュでは、平滑化によってメッシュが縮小する傾向があります。
