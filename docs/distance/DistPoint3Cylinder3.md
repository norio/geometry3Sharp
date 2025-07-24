# DistPoint3Cylinder3

`DistPoint3Cylinder3` クラスは、3Dの点 (`Vector3d`) と有限または無限の円柱 (`Cylinder3d`) の表面との間の最短距離を計算します。

このクラスは、符号付き距離も計算するため、点が円柱の内部にあるか外部にあるかを判別できます。

## 機能

-   **符号付き距離 (`SignedDistance`):** 点から円柱表面までの最短距離を計算します。
    -   点が円柱の外部にある場合、値は正になります。
    -   点が円柱の内部にある場合、値は負になります。
    -   点が円柱の表面上にある場合、値は0になります。
-   **最短距離の二乗 (`DistanceSquared`):** `SignedDistance` の二乗。常に正の値です。
-   **最近接点 (`CylinderClosest`):** 円柱の表面上で、点に最も近い点を特定します。
-   **内外判定 (`IsInside`):** 点が円柱の内部にあるかどうかを返します。
-   **ソリッド距離 (`SolidDistance`):** 点が円柱の内部にある場合は0、外部にある場合は表面までの距離を返します。
-   **無限円柱のサポート:** `Cylinder.Height` が `double.MaxValue` の場合、無限円柱として扱います。

## アルゴリズムの概要

1.  **座標系変換:** 点を、円柱の中心軸がZ軸となるローカル座標系に変換します。
2.  **最近接点の候補計算:**
    -   **側面:** まず、点が無限円柱の側面上のどこに最も近いかを計算します。
    -   **上面/底面:** 次に、ローカルZ座標（高さ）が円柱の高さの範囲 (`[-Height/2, Height/2]`) を超えているかチェックします。
        -   超えている場合、最近接点は円柱の上面または底面の円盤上（側面または端の円周上）になります。
        -   超えていない場合、最近接点は側面上になります。
3.  **内外判定と符号付き距離:**
    -   点が円柱の外部にある場合、計算された距離がそのまま `SignedDistance` となります。
    -   点が円柱の内部にある場合、側面、上面、底面のうち最も近い表面までの距離を計算し、その値に負号を付けたものが `SignedDistance` となります。
4.  **座標系逆変換:** ローカル座標系で求めた最近接点を、ワールド座標系に戻します。

## 使い方

```csharp
// 3Dの点と円柱を定義
Cylinder3d cylinder = new Cylinder3d(
    new Segment3d(new Vector3d(0, -10, 0), new Vector3d(0, 10, 0)), // Axis
    5.0 // Radius
);
Vector3d point = new Vector3d(10, 5, 0); // 円柱の外部の点

// DistPoint3Cylinder3 のインスタンスを作成
DistPoint3Cylinder3 distanceQuery = new DistPoint3Cylinder3(point, cylinder);

// 距離を計算
distanceQuery.Compute();

// 計算結果にアクセス
Console.WriteLine($"Is Inside: {distanceQuery.IsInside}"); // 結果: false
Console.WriteLine($"Signed Distance: {distanceQuery.SignedDistance}"); // 結果: 5.0
Console.WriteLine($"Squared Distance: {distanceQuery.DistanceSquared}"); // 結果: 25.0
Console.WriteLine($"Solid Distance: {distanceQuery.SolidDistance}"); // 結果: 5.0

// 最近接点を取得
Vector3d closestOnCylinder = distanceQuery.CylinderClosest; // 結果: (5, 5, 0)
Console.WriteLine($"Closest point on Cylinder: {closestOnCylinder}");


// --- 点が円柱の内部にある場合 ---
Vector3d insidePoint = new Vector3d(1, 1, 1);
DistPoint3Cylinder3 query2 = new DistPoint3Cylinder3(insidePoint, cylinder);
query2.Compute();

Console.WriteLine($"Is Inside: {query2.IsInside}"); // 結果: true
// 側面までの距離は sqrt(1*1+0*0) = 1 なので、5-1=4。よって-4。
Console.WriteLine($"Signed Distance: {query2.SignedDistance}"); // 結果: -4.0
Console.WriteLine($"Solid Distance: {query2.SolidDistance}"); // 結果: 0.0
```

## プロパティ

-   `Point`: 計算対象の点 (`Vector3d`)。
-   `Cylinder`: 計算対象の円柱 (`Cylinder3d`)。
-   `DistanceSquared`: 最短距離の二乗（常に正）。
-   `SignedDistance`: 符号付き最短距離。
-   `IsInside`: 点が円柱の内部にあるか。
-   `SolidDistance`: 点が内部にある場合は0、外部にある場合は距離。
-   `CylinderClosest`: 円柱表面上の最近接点。

## メソッド

-   `Compute()`: 距離計算を実行し、クラスのインスタンスを返します。
-   `Get()`: `SignedDistance` の絶対値を返します。
-   `GetSquared()`: `DistanceSquared` を返します。
