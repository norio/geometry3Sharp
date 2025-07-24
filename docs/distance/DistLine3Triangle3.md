# DistLine3Triangle3

`DistLine3Triangle3` クラスは、3Dの直線（`Line3d`）と三角形（`Triangle3d`）の間の最短距離を計算します。

## 機能

-   3Dの直線と三角形間の最短距離、およびその二乗値を計算します。
-   直線と三角形が交差するかどうかを判定します。
    -   **交差する場合:** 距離は0になり、最近接点は交点になります。
    -   **交差しない場合:** 最短距離は、直線と三角形の3つの辺のいずれかとの間の距離になります。
-   それぞれのジオメトリ上で、最短距離を与える点（最近接点）を特定します。
-   最近接点に対応する、直線のパラメータと、三角形の重心座標（Barycentric Coordinates）を計算します。

## アルゴリズムの概要

1.  **交差判定:** まず、直線が三角形の平面と交差するかどうかをチェックします。
2.  交差する場合、交点が三角形の内部（または辺上）にあるかを重心座標を用いて判定します。
3.  交点が三角形の内部にあれば、距離は0であり、計算は終了です。
4.  交点が三角形の外部にある場合、または直線が三角形の平面と平行な場合は、最短点は三角形の辺上にあることが保証されます。
5.  `DistLine3Segment3` を使用して、直線と三角形の3つの辺それぞれとの最短距離を計算し、その中で最も小さいものを最終的な結果として採用します。

## 使い方

```csharp
// 3Dの直線と三角形を定義
Line3d line = new Line3d(new Vector3d(1, 10, 1), Vector3d.AxisY); // x=1, z=1 を通るY軸に平行な直線
Triangle3d triangle = new Triangle3d(
    new Vector3d(0, 0, 0),
    new Vector3d(5, 0, 0),
    new Vector3d(0, 0, 5)
);

// DistLine3Triangle3 のインスタンスを作成
DistLine3Triangle3 distanceQuery = new DistLine3Triangle3(line, triangle);

// 距離を計算
double distance = distanceQuery.Get();
double distanceSq = distanceQuery.GetSquared();

// 計算結果にアクセス
Console.WriteLine($"Distance: {distance}"); // 結果: 10.0
Console.WriteLine($"Squared Distance: {distanceSq}"); // 結果: 100.0

// 最近接点を取得
Vector3d closestOnLine = distanceQuery.LineClosest;         // 結果: (1, 10, 1)
Vector3d closestOnTriangle = distanceQuery.TriangleClosest; // 結果: (1, 0, 1)
Console.WriteLine($"Closest point on Line: {closestOnLine}");
Console.WriteLine($"Closest point on Triangle: {closestOnTriangle}");

// パラメータと重心座標を取得
double lineParam = distanceQuery.LineParam;
Vector3d baryCoords = distanceQuery.TriangleBaryCoords;
Console.WriteLine($"Parameter on Line: {lineParam}");
Console.WriteLine($"Barycentric Coords on Triangle: {baryCoords}");
```

## プロパティ

-   `Line`: 計算対象の直線 (`Line3d`)。
-   `Triangle`: 計算対象の三角形 (`Triangle3d`)。
-   `DistanceSquared`: 計算された最短距離の二乗。
-   `LineClosest`: 直線上の最近接点。
-   `TriangleClosest`: 三角形上の最近接点。
-   `LineParam`: 直線上の最近接点に対応するパラメータ `t`。
-   `TriangleBaryCoords`: 三角形上の最近接点に対応する重心座標。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: 最短距離を返します（必要であれば計算を実行します）。
-   `GetSquared()`: 最短距離の二乗を返します（必要であれば計算を実行します）。
