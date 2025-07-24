# DistLine2Segment2

`DistLine2Segment2` クラスは、2Dの直線（`Line2d`）と線分（`Segment2d`）の間の最短距離を計算します。

## 機能

-   2Dの直線と線分間の最短距離、およびその二乗値を計算します。
-   それぞれのジオメトリ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、直線および線分のパラメータを計算します。
-   最近接点が線分の端点になるか、内部の点になるかを自動的に判別して計算します。

## 使い方

```csharp
// 2Dの直線と線分を定義
Line2d line = new Line2d(new Vector2d(0, 5), Vector2d.AxisX); // y = 5
Segment2d segment = new Segment2d(new Vector2d(0, 0), new Vector2d(10, 0)); // y=0, x in [0,10]

// DistLine2Segment2 のインスタンスを作成
DistLine2Segment2 distanceQuery = new DistLine2Segment2(line, segment);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 5.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 25.0

// 最近接点を取得
Vector2d closestOnLine = distanceQuery.LineClosest;       // 結果: (0, 5) or (10,5) or anywhere in between
Vector2d closestOnSegment = distanceQuery.SegmentClosest; // 結果: (0, 0) or (10,0) or anywhere in between
Console.WriteLine($"Closest point on Line: {closestOnLine}");
Console.WriteLine($"Closest point on Segment: {closestOnSegment}");

// パラメータを取得
// 線分の場合、パラメータは中心からの距離で、[-Extent, Extent] の範囲
double lineParam = distanceQuery.LineParameter;
double segParam = distanceQuery.SegmentParameter;
Console.WriteLine($"Parameter on Line: {lineParam}");
Console.WriteLine($"Parameter on Segment: {segParam}");

// 静的メソッドを使用して距離のみを簡単に計算することも可能
double minDistance = DistLine2Segment2.MinDistance(line, segment);
```

### 最近接点が線分の端点になる例

```csharp
Line2d line2 = new Line2d(new Vector2d(20, 0), Vector2d.AxisY); // x = 20
// Segmentは上記と同じ y=0, x in [0,10]

DistLine2Segment2 query2 = new DistLine2Segment2(line2, segment);
query2.Compute();

Console.WriteLine($"Distance: {query2.Get()}"); // 結果: 10.0
Console.WriteLine($"Closest on Line: {query2.LineClosest}");       // 結果: (20, 0)
Console.WriteLine($"Closest on Segment: {query2.SegmentClosest}"); // 結果: (10, 0)
```

## プロパティ

-   `Line`: 計算対象の直線 (`Line2d`)。
-   `Segment`: 計算対象の線分 (`Segment2d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `LineClosest`: 直線上の最近接点。
-   `SegmentClosest`: 線分上の最近接点。
-   `LineParameter`: 直線上の最近接点に対応するパラメータ `t`。
-   `SegmentParameter`: 線分上の最近接点に対応するパラメータ `t`。線分の中心からの距離で、範囲は `[-Extent, Extent]`。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
