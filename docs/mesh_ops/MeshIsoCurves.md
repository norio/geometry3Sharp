# MeshIsoCurves

`MeshIsoCurves` クラスは、メッシュ上にスカラー関数 `ValueF` の等高線（アイソカーブ）を計算します。等高線は、`ValueF(position) == 0` となる点の集合です。

## 機能

- **等高線の計算:** メッシュの各三角形を横切る等高線のセグメントを計算し、それらを `DGraph3` 形式のグラフとして構築します。
- **値の事前計算:** `PrecomputeVertexValues` を `true` に設定すると、メッシュの各頂点での `ValueF` の値をマルチスレッドで事前計算し、パフォーマンスを向上させることができます。
- **ルート探索:** メッシュのエッジ上で `ValueF == 0` となる正確な位置を見つけるために、複数のルート探索モード（`SingleLerp`, `LerpSteps`, `Bisection`）を提供します。
- **グラフ情報の追跡:** `WantGraphEdgeInfo` を `true` にすると、生成されたグラフの各エッジがメッシュのどの三角形やエッジに対応するかといった詳細情報を追跡できます。
- **メッシュの分割:** `SplitAtIsoCrossings()` メソッドを呼び出すことで、計算された等高線の交点でメッシュのエッジを実際に分割できます。

## 使い方

```csharp
// メッシュと、評価したいスカラー関数を定義
DMesh3 mesh = ...;
Func<Vector3d, double> scalarFunc = (position) => {
    // 例: Y座標を値とする
    return position.y - 10.0; // y = 10 の平面が等高線になる
};

// MeshIsoCurves のインスタンスを作成
MeshIsoCurves iso = new MeshIsoCurves(mesh, scalarFunc);

// オプションを設定
iso.PrecomputeVertexValues = true; // 頂点値を事前計算して高速化
iso.RootMode = MeshIsoCurves.RootfindingModes.Bisection; // 高精度なルート探索
iso.RootModeSteps = 8;

// 等高線を計算
iso.Compute();

// 結果のグラフを取得
DGraph3 isoCurvesGraph = iso.Graph;

// DGraph3Util を使って、グラフから連続したカーブ（DCurve3）を抽出
// ShouldReverseGraphEdge を使って、カーブの向きをメッシュと一致させる
var curves = DGraph3Util.ExtractCurves(iso.Graph, iso.ShouldReverseGraphEdge);

foreach (DCurve3 curve in curves.Paths) {
    // 抽出された等高線カーブを処理
}

// 必要であれば、メッシュを等高線で分割
// iso.SplitAtIsoCrossings();
```

## `DGraph3` と `DGraph3Util`

- `Compute()` の結果は `DGraph3` という単純な3Dグラフ構造で返されます。これは頂点と、それらを結ぶ方向のないエッジの集まりです。
- `DGraph3Util.ExtractCurves()` などのユーティリティ関数を使用することで、このグラフから連続した `DCurve3`（ポリライン）や `Polygon2d`（閉じたループ）を抽出できます。
