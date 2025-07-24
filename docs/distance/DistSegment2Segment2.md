# DistSegment2Segment2

`DistSegment2Segment2` クラスは、2つの2D線分（`Segment2d`）間の最短距離を計算します。

## 機能

-   2つの2D線分間の最短距離、およびその二乗値を計算します。
-   それぞれの線分上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、各線分のパラメータを計算します。
-   最近接点が各線分の端点になるか、内部の点になるかを自動的に判別して計算します。

## アルゴリズムの概要

このアルゴリズムは、2つの線分をそれぞれパラメータ `s0` と `s1` を持つ直線とみなし、距離の二乗 `D(s0, s1)` を最小化する問題として定式化します。パラメータの範囲はそれぞれ `[-extent0, extent0]` と `[-extent1, extent1]` に制約されます。

1.  `D` を最小化する `(s0, s1)` を、勾配がゼロになる点を解くことで求めます。
2.  解かれた `(s0, s1)` が、制約領域（矩形領域）の中にあるかを判定します。
3.  **領域0 (Interior):** 解が矩形の内部にあれば、最近接点は両方の線分の内部にあります。
4.  **その他の領域 (Sides/Corners):** 解が矩形の外部にある場合、最短点は境界上にあることが保証されます。パラメータ平面を9つの領域に分割し、それぞれの領域に対応する制約付きの最小化問題を解くことで、正しい最近接点を見つけます。
5.  線分が平行である特殊なケースも処理します。

## 使い方

```csharp
// 2つの2D線分を定義
Segment2d segmentA = new Segment2d(new Vector2d(0, 0), new Vector2d(10, 0)); // X軸上の線分
Segment2d segmentB = new Segment2d(new Vector2d(15, 5), new Vector2d(15, 15)); // x=15上の線分

// DistSegment2Segment2 のインスタンスを作成
DistSegment2Segment2 distanceQuery = new DistSegment2Segment2(segmentA, segmentB);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: sqrt(5^2 + 5^2) = 7.071...
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 50.0

// 最近接点を取得
Vector2d closestOnA = distanceQuery.Segment1Closest; // 結果: (10, 0)
Vector2d closestOnB = distanceQuery.Segment2Closest; // 結果: (15, 5)
Console.WriteLine($"Closest point on Segment A: {closestOnA}");
Console.WriteLine($"Closest point on Segment B: {closestOnB}");

// パラメータを取得 (中心からの距離)
double paramOnA = distanceQuery.Segment1Parameter; // 結果: 5.0 (Extent)
double paramOnB = distanceQuery.Segment2Parameter; // 結果: -5.0 (-Extent)
Console.WriteLine($"Parameter on Segment A: {paramOnA}");
Console.WriteLine($"Parameter on Segment B: {paramOnB}");

// 静的メソッドを使用して距離のみを簡単に計算することも可能
double minDistance = DistSegment2Segment2.MinDistance(segmentA, segmentB);
```

## プロパティ

-   `Segment1`: 1つ目の線分 (`Segment2d`)。
-   `Segment2`: 2つ目の線分 (`Segment2d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `Segment1Closest`: 1つ目の線分上の最近接点。
-   `Segment2Closest`: 2つ目の線分上の最近接点。
-   `Segment1Parameter`: 1つ目の線分上の最近接点に対応するパラメータ `t`（中心からの距離）。
-   `Segment2Parameter`: 2つ目の線分上の最近接点に対応するパラメータ `t`（中心からの距離）。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します。
-   `GetSquared()`: 最短距離の二乗を返します。
