# DistLine3Ray3

`DistLine3Ray3` クラスは、3Dの直線（`Line3d`）と半直線/レイ（`Ray3d`）の間の最短距離を計算します。

## 機能

-   3Dの直線とレイ間の最短距離、およびその二乗値を計算します。
-   それぞれのジオメトリ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、直線およびレイのパラメータを計算します。
-   最近接点がレイの始点になるか、レイの内部の点になるかを自動的に判別して計算します。

## 使い方

```csharp
// 3Dの直線とレイを定義
Line3d line = new Line3d(new Vector3d(0, 10, 0), Vector3d.AxisX); // y=10, z=0 の直線
Ray3d ray = new Ray3d(new Vector3d(5, 0, 0), Vector3d.AxisY);   // (5,0,0) からY+方向へのレイ

// DistLine3Ray3 のインスタンスを作成
DistLine3Ray3 distanceQuery = new DistLine3Ray3(ray, line);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 10.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 100.0

// 最近接点を取得
Vector3d closestOnLine = distanceQuery.LineClosest; // 結果: (5, 10, 0)
Vector3d closestOnRay = distanceQuery.RayClosest;   // 結果: (5, 0, 0)
Console.WriteLine($"Closest point on Line: {closestOnLine}");
Console.WriteLine($"Closest point on Ray: {closestOnRay}");

// パラメータを取得
// レイの場合、パラメータは始点からの距離で、[0, +∞) の範囲
double lineParam = distanceQuery.LineParameter; // 結果: 5.0
double rayParam = distanceQuery.RayParameter;   // 結果: 0.0
Console.WriteLine($"Parameter on Line: {lineParam}");
Console.WriteLine($"Parameter on Ray: {rayParam}");

// 静的メソッドを使用して距離のみを簡単に計算することも可能
double minDistance = DistLine3Ray3.MinDistance(ray, line);
```

## プロパティ

-   `Line`: 計算対象の直線 (`Line3d`)。
-   `Ray`: 計算対象のレイ (`Ray3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `LineClosest`: 直線上の最近接点。
-   `RayClosest`: レイ上の最近接点。
-   `LineParameter`: 直線上の最近接点に対応するパラメータ `t`。
-   `RayParameter`: レイ上の最近接点に対応するパラメータ `t`。レイの始点からの距離で、常に非負の値 (`>= 0`)。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
