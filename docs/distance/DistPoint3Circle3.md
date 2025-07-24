# DistPoint3Circle3

`DistPoint3Circle3` クラスは、3Dの点 (`Vector3d`) と3D空間内の円 (`Circle3d`) の間の最短距離を計算します。

## 機能

-   点と円周上の点との間の最短距離、およびその二乗値を計算します。
-   円周上で、点に最も近い点（最近接点）を特定します。
-   点が円の中心軸上にある特殊なケースを処理します。

## アルゴリズムの概要

1.  **平面への射影:** まず、点を円が存在する平面に射影します。これは、点から円の中心へのベクトル `(P - C)` を計算し、そのベクトルから平面の法線に沿った成分を取り除くことで行われます。
2.  **最近接点の決定:**
    -   射影点が円の中心と一致しない場合、円の中心から射影点に向かう方向ベクトルを正規化し、円の半径を掛け合わせることで、円周上の最近接点 `CircleClosest` を見つけます。
    -   射影点が円の中心に一致する場合（つまり、元の点が円の中心軸上にある場合）、円周上のすべての点が等距離になります。この場合、`AllCirclePointsEquidistant` が `true` に設定され、代表として1つの点（中心 + 半径 * 平面X軸）が `CircleClosest` として返されます。
3.  **距離の計算:** 元の点と、計算された円周上の最近接点との間の距離を計算します。

## 使い方

```csharp
// 3Dの点と円を定義
Circle3d circle = new Circle3d(
    new Vector3d(0, 0, 0),    // Center
    Vector3d.AxisZ,          // Normal (円はXY平面上にある)
    5.0                      // Radius
);
Vector3d point = new Vector3d(10, 0, 20); // 円から離れた点

// DistPoint3Circle3 のインスタンスを作成
DistPoint3Circle3 distanceQuery = new DistPoint3Circle3(point, circle);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // sqrt( (10-5)^2 + 20^2 ) = sqrt(25+400) = 20.6155...
Console.WriteLine($"Squared Distance: {distanceSq}"); // 425.0

// 最近接点を取得
Vector3d closestOnCircle = distanceQuery.CircleClosest; // 結果: (5, 0, 0)
Console.WriteLine($"Closest point on Circle: {closestOnCircle}");


// --- 点が円の中心軸上にある場合 ---
Vector3d pointOnAxis = new Vector3d(0, 0, 30);
DistPoint3Circle3 query2 = new DistPoint3Circle3(pointOnAxis, circle);
query2.Compute();

Console.WriteLine($"Distance: {query2.Get()}"); // sqrt(5^2 + 30^2) = 30.41...
Console.WriteLine($"All points equidistant: {query2.AllCirclePointsEquidistant}"); // true
Console.WriteLine($"Closest point on Circle: {query2.CircleClosest}"); // (5,0,0) - PlaneX方向の点
```

## プロパティ

-   `Point`: 計算対象の点 (`Vector3d`)。
-   `Circle`: 計算対象の円 (`Circle3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `CircleClosest`: 円周上の最近接点。
-   `AllCirclePointsEquidistant`: 点が円の中心軸上にあり、円周上のすべての点が等距離であるかどうかを示すフラグ。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
