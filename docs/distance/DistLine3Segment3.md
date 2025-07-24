# DistLine3Segment3

`DistLine3Segment3` クラスは、3Dの直線（`Line3d`）と線分（`Segment3d`）の間の最短距離を計算します。

## 機能

-   3Dの直線と線分間の最短距離、およびその二乗値を計算します。
-   それぞれのジオメトリ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、直線および線分のパラメータを計算します。
-   最近接点が線分の端点になるか、内部の点になるかを自動的に判別して計算します。

## 使い方

```csharp
// 3Dの直線と線分を定義
Line3d line = new Line3d(new Vector3d(0, 10, 0), Vector3d.AxisX); // y=10, z=0 の直線
Segment3d segment = new Segment3d(new Vector3d(0, 0, 0), new Vector3d(10, 0, 0)); // y=0, z=0, x in [0,10] の線分

// DistLine3Segment3 のインスタンスを作成
DistLine3Segment3 distanceQuery = new DistLine3Segment3(line, segment);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 10.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 100.0

// 最近接点を取得
Vector3d closestOnLine = distanceQuery.LineClosest;       // 結果: (5, 10, 0)
Vector3d closestOnSegment = distanceQuery.SegmentClosest; // 結果: (5, 0, 0)
Console.WriteLine($"Closest point on Line: {closestOnLine}");
Console.WriteLine($"Closest point on Segment: {closestOnSegment}");

// パラメータを取得
// 線分の場合、パラメータは中心からの距離で、[-Extent, Extent] の範囲
double lineParam = distanceQuery.LineParameter;   // 結果: 5.0
double segParam = distanceQuery.SegmentParameter; // 結果: 0.0
Console.WriteLine($"Parameter on Line: {lineParam}");
Console.WriteLine($"Parameter on Segment: {segParam}");

// 静的メソッドを使用して距離のみを簡単に計算することも可能
double minDistance = DistLine3Segment3.MinDistance(line, segment);
```

## プロパティ

-   `Line`: 計算対象の直線 (`Line3d`)。
-   `Segment`: 計算対象の線分 (`Segment3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `LineClosest`: 直線上の最近接点。
-   `SegmentClosest`: 線分上の最近接点。
-   `LineParameter`: 直線上の最近接点に対応するパラメータ `t`。
-   `SegmentParameter`: 線分上の最近接点に対応するパラメータ `t`。線分の中心からの距離で、範囲は `[-Extent, Extent]`。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
