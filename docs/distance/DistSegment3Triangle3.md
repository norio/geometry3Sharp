# DistSegment3Triangle3

`DistSegment3Triangle3` クラスは、3Dの線分（`Segment3d`）と三角形（`Triangle3d`）の間の最短距離を計算します。

## 機能

-   3Dの線分と三角形間の最短距離、およびその二乗値を計算します。
-   それぞれのジオメトリ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、線分のパラメータと、三角形の重心座標（Barycentric Coordinates）を計算します。

## アルゴリズムの概要

このクラスは、既存の距離計算クラスを組み合わせて効率的に計算を行います。

1.  **直線と三角形の距離:** まず、線分を無限の直線（`Line3d`）とみなし、`DistLine3Triangle3` を使って直線と三角形の間の最短距離を計算します。この結果、直線上での最近接点のパラメータ `LineParam` が得られます。
2.  **パラメータのチェック:**
    -   得られた `LineParam` が線分の範囲内（`[-Extent, Extent]`）にある場合、直線と三角形の最近接点が、そのまま線分と三角形の最近接点となります。
    -   `LineParam` が線分の範囲外にある場合、最短点は線分のどちらかの端点と三角形の間にあることが保証されます。
3.  **点と三角形の距離:** `LineParam` が範囲外だった場合、`LineParam` が示す側の線分の端点（`P0` または `P1`）を選択します。そして、`DistPoint3Triangle3` を使用して、その端点と三角形の間の最短距離を計算します。これが最終的な結果となります。

## 使い方

```csharp
// 3Dの線分と三角形を定義
Segment3d segment = new Segment3d(
    new Vector3d(2, 20, 2),
    new Vector3d(2, 30, 2)
);
Triangle3d triangle = new Triangle3d(
    new Vector3d(0, 0, 0),
    new Vector3d(10, 0, 0),
    new Vector3d(0, 0, 10)
);

// DistSegment3Triangle3 のインスタンスを作成
DistSegment3Triangle3 distanceQuery = new DistSegment3Triangle3(segment, triangle);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 20.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 400.0

// 最近接点を取得
Vector3d closestOnSegment = distanceQuery.SegmentClosest;     // 結果: (2, 20, 2)
Vector3d closestOnTriangle = distanceQuery.TriangleClosest;   // 結果: (2, 0, 2)
Console.WriteLine($"Closest point on Segment: {closestOnSegment}");
Console.WriteLine($"Closest point on Triangle: {closestOnTriangle}");

// パラメータと重心座標を取得
double segParam = distanceQuery.SegmentParam; // 結果: -5.0 (-Extent)
Vector3d baryCoords = distanceQuery.TriangleBaryCoords;
Console.WriteLine($"Parameter on Segment: {segParam}");
Console.WriteLine($"Barycentric Coords on Triangle: {baryCoords}");
```

## プロパティ

-   `Segment`: 計算対象の線分 (`Segment3d`)。
-   `Triangle`: 計算対象の三角形 (`Triangle3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `SegmentClosest`: 線分上の最近接点。
-   `TriangleClosest`: 三角形上の最近接点。
-   `SegmentParam`: 線分上の最近接点に対応するパラメータ `t`（中心からの距離）。
-   `TriangleBaryCoords`: 三角形上の最近接点に対応する重心座標。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
