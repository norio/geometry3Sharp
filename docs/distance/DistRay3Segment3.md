# DistRay3Segment3

`DistRay3Segment3` クラスは、3Dの半直線/レイ（`Ray3d`）と線分（`Segment3d`）の間の最短距離を計算します。

## 機能

-   3Dのレイと線分間の最短距離、およびその二乗値を計算します。
-   それぞれのジオメトリ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、レイおよび線分のパラメータを計算します。
-   最近接点がレイの始点になるか、線分の端点になるか、あるいは両方の内部点になるかを自動的に判別して計算します。

## アルゴリズムの概要

このアルゴリズムは、レイをパラメータ `s0 (>= 0)` を持つ直線、線分をパラメータ `s1 (in [-extent, extent])` を持つ直線とみなし、距離の二乗 `D(s0, s1)` を最小化する問題として定式化します。

1.  `D` を最小化する `(s0, s1)` を、勾配がゼロになる点を解くことで求めます。
2.  解かれた `(s0, s1)` が、制約領域 `s0 >= 0` かつ `s1 in [-extent, extent]` の中にあるかを判定します。
3.  **領域0:** 解が制約領域の内部にあれば、最近接点はレイと線分の両方の内部にあります。
4.  **その他の領域:** 解が制約領域の外にある場合、最短点は境界上にあることが保証されます。パラメータ平面を9つの領域に分割し、それぞれの領域に対応する制約付きの最小化問題を解くことで、正しい最近接点を見つけます。
5.  レイと線分が平行である特殊なケースも処理します。

## 使い方

```csharp
// 3Dのレイと線分を定義
Ray3d ray = new Ray3d(new Vector3d(0, 0, 0), Vector3d.AxisX); // X軸の正方向
Segment3d segment = new Segment3d(
    new Vector3d(5, 10, 0),
    new Vector3d(5, -10, 0)
); // x=5, z=0 上の線分

// DistRay3Segment3 のインスタンスを作成
DistRay3Segment3 distanceQuery = new DistRay3Segment3(ray, segment);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 10.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 100.0

// 最近接点を取得
Vector3d closestOnRay = distanceQuery.RayClosest;       // 結果: (5, 0, 0)
Vector3d closestOnSegment = distanceQuery.SegmentClosest; // 結果: (5, 0, 0)
Console.WriteLine($"Closest point on Ray: {closestOnRay}");
Console.WriteLine($"Closest point on Segment: {closestOnSegment}");

// パラメータを取得
double rayParam = distanceQuery.RayParameter;     // 結果: 5.0
double segParam = distanceQuery.SegmentParameter; // 結果: 0.0
Console.WriteLine($"Parameter on Ray: {rayParam}");
Console.WriteLine($"Parameter on Segment: {segParam}");

// 静的メソッドを使用してインスタンスを作成せずに計算することも可能
double r_t, s_t;
double distSqr = DistRay3Segment3.SquaredDistance(ref ray, ref segment, out r_t, out s_t);
```

## プロパティ

-   `Ray`: 計算対象のレイ (`Ray3d`)。
-   `Segment`: 計算対象の線分 (`Segment3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `RayClosest`: レイ上の最近接点。
-   `SegmentClosest`: 線分上の最近接点。
-   `RayParameter`: レイ上の最近接点に対応するパラメータ `t`（始点からの距離）。
-   `SegmentParameter`: 線分上の最近接点に対応するパラメータ `t`（中心からの距離）。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します。
-   `GetSquared()`: 最短距離の二乗を返します。
-   `SquaredDistance(ref ...)`: インスタンスを作成せずに直接計算を行う静的メソッド。
