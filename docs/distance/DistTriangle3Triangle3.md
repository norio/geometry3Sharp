# DistTriangle3Triangle3

`DistTriangle3Triangle3` クラスは、2つの3D三角形（`Triangle3d`）間の最短距離を計算します。

## 機能

-   2つの3D三角形間の最短距離、およびその二乗値を計算します。
-   それぞれの三角形上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、各三角形の重心座標（Barycentric Coordinates）を計算します。

## アルゴリズムの概要

このアルゴリズムは、2つの三角形が交差していない場合、最短距離は一方の三角形の辺と、もう一方の三角形の面との間に生じるという事実に基づいています。

1.  **Triangle0の辺 vs Triangle1の面:**
    -   `triangle0` の3つの各辺について、`DistSegment3Triangle3` を使用して `triangle1` との最短距離を計算します。
    -   これら3つの計算結果のうち、最も小さい距離を現在の最短距離 `sqrDist` として保持します。
    -   この過程で距離がゼロ（または許容誤差内）になれば、三角形同士が交差していると判断し、計算を終了します。

2.  **Triangle1の辺 vs Triangle0の面:**
    -   同様に、`triangle1` の3つの各辺について、`DistSegment3Triangle3` を使用して `triangle0` との最短距離を計算します。
    -   計算結果が現在の最短距離 `sqrDist` よりも小さい場合は、最短距離と最近接点の情報を更新します。

3.  **最終結果:** すべての辺と面のペアの比較が終わった時点で、保持されている `sqrDist` が2つの三角形間の最短距離の二乗となります。

このアプローチは、点と三角形の間の距離を計算する必要がないため、より効率的です（2つの三角形の最近接点が両方とも頂点である場合は、辺と面の比較に含まれるため）。

## 使い方

```csharp
// 2つの3D三角形を定義
Triangle3d triangleA = new Triangle3d(
    new Vector3d(0, 0, 0),
    new Vector3d(10, 0, 0),
    new Vector3d(0, 10, 0)
);
Triangle3d triangleB = new Triangle3d(
    new Vector3d(2, 2, 5),
    new Vector3d(12, 2, 5),
    new Vector3d(2, 12, 5)
);

// DistTriangle3Triangle3 のインスタンスを作成
DistTriangle3Triangle3 distanceQuery = new DistTriangle3Triangle3(triangleA, triangleB);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 5.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 25.0

// 最近接点を取得
Vector3d closestOnA = distanceQuery.Triangle0Closest; // 結果: (2, 2, 0)
Vector3d closestOnB = distanceQuery.Triangle1Closest; // 結果: (2, 2, 5)
Console.WriteLine($"Closest point on Triangle A: {closestOnA}");
Console.WriteLine($"Closest point on Triangle B: {closestOnB}");

// 重心座標を取得
Vector3d baryOnA = distanceQuery.Triangle0BaryCoords;
Vector3d baryOnB = distanceQuery.Triangle1BaryCoords;
Console.WriteLine($"Barycentric Coords on A: {baryOnA}");
Console.WriteLine($"Barycentric Coords on B: {baryOnB}");
```

## プロパティ

-   `Triangle0`: 1つ目の三角形 (`Triangle3d`)。
-   `Triangle1`: 2つ目の三角形 (`Triangle3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `Triangle0Closest`: 1つ目の三角形上の最近接点。
-   `Triangle1Closest`: 2つ目の三角形上の最近接点。
-   `Triangle0BaryCoords`: 1つ目の三角形上の最近接点に対応する重心座標。
-   `Triangle1BaryCoords`: 2つ目の三角形上の最近接点に対応する重心座標。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
