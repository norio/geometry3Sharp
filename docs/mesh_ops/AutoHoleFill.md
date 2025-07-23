# AutoHoleFill

`AutoHoleFill` クラスは、メッシュの穴を自動的に埋めるための機能を提供します。穴の形状を分析し、最適な穴埋め戦略を選択して実行します。

## 機能

- 穴の形状に応じて、以下の穴埋め戦略を自動的に選択します。
  - `PlanarFill`: 平面的な穴を埋めます。
  - `MinimalFill`: 最小面積のサーフェスで穴を埋めます。
  - `PlanarSpansFill`: 平面的なスパンスパンに基づいて穴を埋めます（プロトタイプ）。
  - `SmoothFill`: スムーズなサーフェスで穴を埋めます。
- `TargetEdgeLength` プロパティを使用して、穴埋めメッシュの目標エッジ長を指定できます。

## 使い方

```csharp
// メッシュと穴の境界ループを取得
DMesh3 mesh = ...;
EdgeLoop fillLoop = ...;

// AutoHoleFill のインスタンスを作成
AutoHoleFill holeFiller = new AutoHoleFill(mesh, fillLoop);

// 目標エッジ長を設定 (オプション)
holeFiller.TargetEdgeLength = 5.0;

// 穴埋めを実行
bool success = holeFiller.Apply();

if (success) {
    // 穴埋めに使用された三角形を取得
    int[] fillTriangles = holeFiller.FillTriangles;
    // ...
}
```
