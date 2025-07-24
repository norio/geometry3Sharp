# DistPoint2Circle2

`DistPoint2Circle2` クラスは、2Dの点 (`Vector2d`) と円 (`Circle2d`) の間の最短距離を計算します。

## 機能

-   点と円周上の点との間の最短距離、およびその二乗値を計算します。
-   円周上で、点に最も近い点（最近接点）を特定します。
-   点が円の中心と一致する特殊なケースを処理します。

## アルゴリズムの概要

1.  **方向ベクトルの計算:** 点から円の中心へ向かうベクトル `(P - C)` を計算します。
2.  **最近接点の決定:**
    -   点が円の中心と一致しない場合、方向ベクトルを正規化し、円の半径を掛けることで、円の中心から点に向かう方向にある円周上の点を求めます。これが最近接点 `CircleClosest` となります。
    -   点が円の中心に一致する場合、円周上のすべての点が等距離にあります。この場合、`AllCirclePointsEquidistant` が `true` に設定され、代表として1つの点（中心 + 半径 * (1,0)）が `CircleClosest` として返されます。
3.  **距離の計算:** 元の点と、計算された最近接点との間の距離を計算します。

## 使い方

```csharp
// 2Dの点と円を定義
Vector2d point = new Vector2d(10, 0);
Circle2d circle = new Circle2d(Vector2d.Zero, 5.0); // 中心(0,0), 半径5

// DistPoint2Circle2 のインスタンスを作成
DistPoint2Circle2 distanceQuery = new DistPoint2Circle2(point, circle);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 5.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 25.0

// 最近接点を取得
Vector2d closestOnCircle = distanceQuery.CircleClosest; // 結果: (5, 0)
Console.WriteLine($"Closest point on Circle: {closestOnCircle}");


// --- 点が円の中心にある場合 ---
Vector2d centerPoint = Vector2d.Zero;
DistPoint2Circle2 query2 = new DistPoint2Circle2(centerPoint, circle);
query2.Compute();

Console.WriteLine($"Distance: {query2.Get()}"); // 結果: 5.0
Console.WriteLine($"All points equidistant: {query2.AllCirclePointsEquidistant}"); // 結果: true
Console.WriteLine($"Closest point on Circle: {query2.CircleClosest}"); // 結果: (5, 0)
```

## プロパティ

-   `Point`: 計算対象の点 (`Vector2d`)。
-   `Circle`: 計算対象の円 (`Circle2d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `CircleClosest`: 円周上の最近接点。
-   `AllCirclePointsEquidistant`: 点が円の中心にあり、円周上のすべての点が等距離であるかどうかを示すフラグ。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
