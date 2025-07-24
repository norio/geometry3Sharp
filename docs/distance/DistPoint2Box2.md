# DistPoint2Box2

`DistPoint2Box2` クラスは、2Dの点 (`Vector2d`) と方向性のある矩形 (`Box2d`) の間の最短距離を計算します。

## 機能

-   点と矩形間の最短距離、およびその二乗値を計算します。
-   矩形上で、点に最も近い点（最近接点）を特定します。
-   点が矩形の内部にある場合、距離は0になり、最近接点は点自身と同じ位置になります。
-   点が矩形の外部にある場合、距離は点から矩形の境界までの最短距離になり、最近接点はその境界上の点になります。

## アルゴリズムの概要

1.  **座標系の変換:** 点を矩形のローカル座標系（中心が原点、軸が座標軸と一致）に変換します。
2.  **最近接点の計算:** ローカル座標系で、変換された点の各座標値を矩形の広がり (`Extent`) の範囲内にクランプ（制限）します。これにより、矩形領域内で最も近い点が求まります。
3.  **距離の計算:** 変換された点と、クランプされた最近接点との間の距離（の二乗）を計算します。各座標軸でクランプが発生した場合、その差分（デルタ）の二乗が距離に加算されます。
4.  **座標系の逆変換:** ローカル座標系で求めた最近接点を、ワールド座標系に戻します。

## 使い方

```csharp
// 2Dの点と方向性のある矩形を定義
Vector2d point = new Vector2d(15, 5);
Box2d box = new Box2d(
    new Vector2d(5, 5),      // Center
    new Vector2d(1, 0),      // AxisX
    new Vector2d(0, 1),      // AxisY
    new Vector2d(5, 2)       // Extent (half-dimensions)
); // この矩形は x=[0,10], y=[3,7] の範囲を占める

// DistPoint2Box2 のインスタンスを作成
DistPoint2Box2 distanceQuery = new DistPoint2Box2(point, box);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 5.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 25.0

// 最近接点を取得
Vector2d closestOnBox = distanceQuery.BoxClosest; // 結果: (10, 5)
Console.WriteLine($"Closest point on Box: {closestOnBox}");
```

## プロパティ

-   `Point`: 計算対象の点 (`Vector2d`)。
-   `Box`: 計算対象の矩形 (`Box2d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `BoxClosest`: 矩形上の最近接点。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
