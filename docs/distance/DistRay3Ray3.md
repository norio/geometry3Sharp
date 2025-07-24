# DistRay3Ray3

`DistRay3Ray3` クラスは、2つの3D半直線/レイ（`Ray3d`）間の最短距離を計算します。

## 機能

-   2つの3Dレイ間の最短距離、およびその二乗値を計算します。
-   それぞれのレイ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、各レイのパラメータ（始点からの距離）を計算します。
-   最近接点が各レイの始点になるか、内部の点になるかを自動的に判別して計算します。

## アルゴリズムの概要

このアルゴリズムは、2つのレイをパラメータ `s0` と `s1` を持つ直線とみなし、距離の二乗 `D(s0, s1)` を最小化する問題として定式化します。

1.  `D` を最小化する `(s0, s1)` を、勾配がゼロになる点を解くことで求めます。
2.  解かれた `(s0, s1)` が `s0 >= 0` かつ `s1 >= 0` の領域（第1象限）にあれば、最近接点は両方のレイの内部にあります。
3.  解がこの領域の外にある場合、最短点は境界（`s0=0` または `s1=0` の軸上）または角（`s0=0, s1=0` の原点）にあることが保証されます。
4.  それぞれのケース（内部、辺、角）に応じて、制約付きの最小化問題を解き、最終的な `s0` と `s1` を決定します。
5.  レイが平行である特殊なケースも処理します。

## 使い方

```csharp
// 2つの3Dレイを定義
Ray3d rayA = new Ray3d(new Vector3d(0, 0, 0), Vector3d.AxisX); // X軸の正方向
Ray3d rayB = new Ray3d(new Vector3d(5, 10, 0), Vector3d.AxisY); // (5,10,0)からY軸の正方向

// DistRay3Ray3 のインスタンスを作成
DistRay3Ray3 distanceQuery = new DistRay3Ray3(rayA, rayB);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 10.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 100.0

// 最近接点を取得
Vector3d closestOnA = distanceQuery.Ray1Closest; // 結果: (5, 0, 0)
Vector3d closestOnB = distanceQuery.Ray2Closest; // 結果: (5, 10, 0)
Console.WriteLine($"Closest point on Ray A: {closestOnA}");
Console.WriteLine($"Closest point on Ray B: {closestOnB}");

// パラメータを取得 (始点からの距離、常に非負)
double paramOnA = distanceQuery.Ray1Parameter; // 結果: 5.0
double paramOnB = distanceQuery.Ray2Parameter; // 結果: 0.0
Console.WriteLine($"Parameter on Ray A: {paramOnA}");
Console.WriteLine($"Parameter on Ray B: {paramOnB}");

// 静的メソッドを使用して距離のみを簡単に計算することも可能
double minDistance = DistRay3Ray3.MinDistance(rayA, rayB);
```

## プロパティ

-   `Ray1`: 1つ目のレイ (`Ray3d`)。
-   `Ray2`: 2つ目のレイ (`Ray3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `Ray1Closest`: 1つ目のレイ上の最近接点。
-   `Ray2Closest`: 2つ目のレイ上の最近接点。
-   `Ray1Parameter`: 1つ目のレイ上の最近接点に対応するパラメータ `t`（始点からの距離）。
-   `Ray2Parameter`: 2つ目のレイ上の最近接点に対応するパラメータ `t`（始点からの距離）。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
