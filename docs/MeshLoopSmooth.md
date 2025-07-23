# MeshLoopSmooth

`MeshLoopSmooth` クラスは、メッシュ上のエッジループ（`EdgeLoop`）を平滑化（スムージング）する機能を提供します。

## 機能

- **反復平滑化:** 指定された `Rounds` の数だけ、平滑化処理を繰り返します。各頂点は、ループ上の隣接する2つの頂点の中間点に向かって移動します。
- **平滑化係数:** `Alpha` プロパティで平滑化の強さを制御します（0は変化なし、1は中間点へ完全に移動）。
- **投影機能:** `ProjectF` デリゲートを設定することで、各平滑化ステップの後に、計算された新しい頂点位置を別のサーフェスや位置に再投影することができます。これにより、ループの形状を拘束しながら平滑化を行えます。
- **並列処理:** 平滑化計算は `gParallel.ForEach` を使用して並列で実行され、パフォーマンスが向上します。

## 使い方

```csharp
// 平滑化したいメッシュとエッジループを取得
DMesh3 mesh = ...;
EdgeLoop loop = ...; // メッシュ上の有効なエッジループ

// MeshLoopSmooth のインスタンスを作成
MeshLoopSmooth smoother = new MeshLoopSmooth(mesh, loop);

// パラメータを設定
smoother.Rounds = 30; // 30回反復
smoother.Alpha = 0.5; // 平滑化の強さ

// (オプション) 投影関数を設定
// 例: Y=0 の平面に投影する
// smoother.ProjectF = (pos, vid) => {
//     return new Vector3d(pos.x, 0, pos.z);
// };

// 平滑化処理を実行
bool success = smoother.Smooth();

if (success) {
    // 平滑化が成功しました
    // メッシュは直接更新されています
}
```

## 注意点

- このクラスは、ループ上の頂点のみを移動させます。ループ周辺のメッシュジオメトリは変更されません。
- `ProjectF` を使用しない場合、ループは収縮する傾向があります。
- 入力 `EdgeLoop` は、メッシュ上の有効なエッジループである必要があります。`Validate()` メソッドで確認できます。
