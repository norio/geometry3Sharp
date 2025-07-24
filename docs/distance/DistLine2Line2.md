# DistLine2Line2

`DistLine2Line2` クラスは、2つの2D直線（`Line2d`）間の最短距離を計算します。

## 機能

-   2つの2D直線間の最短距離、およびその二乗値を計算します。
-   それぞれの直線上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、各直線のパラメータ（原点からの距離）を計算します。
-   2つの直線が平行であるか、交差するかにかかわらず、正しく距離を計算します。
    -   **交差する場合:** 距離は0になり、最近接点は交点になります。
    -   **平行な場合:** 距離は2直線間の垂直距離になり、最近接点のペアは無数に存在しますが、そのうちの1ペアを計算します。

## 使い方

```csharp
// 2つの2D直線を定義
Line2d lineA = new Line2d(new Vector2d(0, 0), Vector2d.AxisX); // y = 0
Line2d lineB = new Line2d(new Vector2d(5, 10), Vector2d.AxisY); // x = 5

// DistLine2Line2 のインスタンスを作成
DistLine2Line2 distanceQuery = new DistLine2Line2(lineA, lineB);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}");
Console.WriteLine($"Squared Distance: {distanceSq}");

// 最近接点を取得
Vector2d closestPointOnA = distanceQuery.Line1Closest; // 結果: (5, 0)
Vector2d closestPointOnB = distanceQuery.Line2Closest; // 結果: (5, 0)
Console.WriteLine($"Closest point on Line A: {closestPointOnA}");
Console.WriteLine($"Closest point on Line B: {closestPointOnB}");

// パラメータを取得
double paramOnA = distanceQuery.Line1Parameter; // 結果: 5.0
double paramOnB = distanceQuery.Line2Parameter; // 結果: -10.0
Console.WriteLine($"Parameter on Line A: {paramOnA}");
Console.WriteLine($"Parameter on Line B: {paramOnB}");

// 静的メソッドを使用して距離のみを簡単に計算することも可能
double minDistance = DistLine2Line2.MinDistance(lineA, lineB);
```

## プロパティ

-   `Line`: 1つ目の直線 (`Line2d`)。
-   `Line2`: 2つ目の直線 (`Line2d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。`Get()` または `GetSquared()` が呼び出されるまで -1.0。
-   `Line1Closest`: 1つ目の直線上の最近接点。
-   `Line2Closest`: 2つ目の直線上の最近接点。
-   `Line1Parameter`: 1つ目の直線上の最近接点に対応するパラメータ `t` (`Origin + t * Direction`)。
-   `Line2Parameter`: 2つ目の直線上の最近接点に対応するパラメータ `t`。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
