# DistPoint3Triangle3

`DistPoint3Triangle3` クラスは、3Dの点 (`Vector3d`) と三角形 (`Triangle3d`) の間の最短距離を計算します。

## 機能

-   点と三角形間の最短距離、およびその二乗値を計算します。
-   三角形上で、点に最も近い点（最近接点）を特定します。
-   最近接点に対応する、三角形の重心座標（Barycentric Coordinates）を計算します。
-   最近接点が三角形の内部、辺上、頂点のいずれにあるかを自動的に判別して計算します。

## アルゴリズムの概要

このクラスは、点から三角形への最短距離を求めるための古典的で効率的なアルゴリズムを実装しています。これは、点を三角形の平面に射影し、その射影点が三角形のどのボロノイ領域（内部、辺の外側、頂点の外側）に属するかを判定することで行われます。

1.  **座標変換:** 点を、三角形の頂点 `V0` を原点とし、辺 `V1-V0` と `V2-V0` を基底ベクトルとする座標系に変換します。
2.  **ボロノイ領域の判定:** 変換された座標 `(s, t)` を用いて、点がどの領域にあるかを判定します。
    -   `s >= 0`, `t >= 0`, `s + t <= 1`: 点は三角形の内部に射影されます。最近接点は射影点そのものです。
    -   それ以外の場合: 点は三角形の外部のいずれかの領域に射影されます。この場合、最近接点は三角形の最も近い辺または頂点になります。
3.  **最近接点の計算:** 各領域に対応する計算式を用いて、最近接点と距離の二乗を求めます。
4.  **結果の保存:** 計算された最近接点と重心座標をプロパティに保存します。

## 使い方

```csharp
// 3Dの点と三角形を定義
Triangle3d triangle = new Triangle3d(
    new Vector3d(0, 0, 0),
    new Vector3d(10, 0, 0),
    new Vector3d(0, 10, 0)
);
Vector3d point = new Vector3d(2, 2, 5); // 三角形の真上にある点

// DistPoint3Triangle3 のインスタンスを作成
DistPoint3Triangle3 distanceQuery = new DistPoint3Triangle3(point, triangle);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 5.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 25.0

// 最近接点を取得
Vector3d closestOnTriangle = distanceQuery.TriangleClosest; // 結果: (2, 2, 0)
Console.WriteLine($"Closest point on Triangle: {closestOnTriangle}");

// 重心座標を取得
Vector3d baryCoords = distanceQuery.TriangleBaryCoords; // 結果: (0.6, 0.2, 0.2)
Console.WriteLine($"Barycentric Coords on Triangle: {baryCoords}");

// 静的メソッドを使用して、インスタンスを作成せずに計算することも可能
Vector3d closest, bary;
double distSqr = DistPoint3Triangle3.DistanceSqr(ref point, ref triangle, out closest, out bary);
```

## プロパティ

-   `Point`: 計算対象の点 (`Vector3d`)。
-   `Triangle`: 計算対象の三角形 (`Triangle3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `TriangleClosest`: 三角形上の最近接点。
-   `TriangleBaryCoords`: 三角形上の最近接点に対応する重心座標。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
-   `DistanceSqr(ref ...)`: インスタンスを作成せずに直接計算を行う静的メソッド。パフォーマンスが重要な場合に有効です。
