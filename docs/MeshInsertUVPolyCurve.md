# MeshInsertUVPolyCurve

`MeshInsertUVPolyCurve` クラスは、2Dのポリラインまたはポリゴンを、メッシュのUV空間（デフォルトではXY平面）に挿入（刻印）します。メッシュの既存のエッジを分割し、新しい頂点を追加することで、曲線をメッシュジオメトリに埋め込みます。

## 機能

- **コーナーの挿入:** `Curve` の各頂点をメッシュに挿入します。頂点が既存の三角形の内部にある場合は面をポーク（poke）し、エッジ上にある場合はエッジを分割します。
- **セグメントの挿入:** `Curve` の各セグメントと交差するメッシュエッジを検出し、交点で分割します。
- **結果のパスの抽出:** 挿入操作によって生成されたエッジを追跡し、`EdgeLoop`（閉じたループ）または `EdgeSpan`（開いたパス）として結果を抽出します。
- **単純化:** `Simplify()` メソッドを呼び出すことで、挿入によって生成された余分な（共線上の）頂点を削除し、曲線を単純化できます。
- **UV座標のカスタマイズ:** `PointF` および `SetPointF` デリゲートをオーバーライドすることで、デフォルトのXY座標以外をUV空間として使用できます。

## 使い方

```csharp
// UV空間（XY平面）にジオメトリを持つメッシュと、挿入したい2Dポリラインを取得
DMesh3 mesh = ...;
PolyLine2d polyline = new PolyLine2d(...);

// MeshInsertUVPolyCurve のインスタンスを作成
MeshInsertUVPolyCurve inserter = new MeshInsertUVPolyCurve(mesh, polyline);

// ポリゴン（閉じたループ）を挿入する場合は isLoop を true にするか、
// Polygon2d を受け取るコンストラクタを使用します。
// MeshInsertUVPolyCurve inserter = new MeshInsertUVPolyCurve(mesh, polygon, true);

// 挿入処理を実行
bool success = inserter.Apply();

if (success) {
    // 挿入が成功しました

    // 挿入されたエッジのセットを取得
    HashSet<int> insertedEdges = inserter.OnCutEdges;

    // 挿入されたループまたはスパンを取得
    List<EdgeLoop> loops = inserter.Loops;
    List<EdgeSpan> spans = inserter.Spans;

    // 挿入されたカーブを単純化する (オプション)
    inserter.Simplify();

    // 単純化後のループを取得
    EdgeLoop simplifiedLoop = inserter.Loops[0];
}
```

## 注意点

- このクラスは、入力メッシュが縮退した三角形やエッジを含まないことを前提としています。`Validate()` メソッドで簡単なチェックが可能です。
- `Curve` のセグメントがメッシュの既存のエッジとほぼ平行で非常に近い場合、ロバスト性の問題が発生する可能性があります。
