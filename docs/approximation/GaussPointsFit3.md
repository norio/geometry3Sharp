# GaussPointsFit3

`GaussPointsFit3` クラスは、3D点群にガウス分布をフィッティングします。この処理は、主成分分析（PCA）として知られる手法と等価です。

結果は、点群の分布を表す方向付きバウンディングボックス (`Box3d`) として返されます。

## 機能

-   **中心 (Mean):** フィットしたボックスの中心 (`Box.Center`) は、入力された点群の平均（重心）になります。
-   **主軸 (Axes):** ボックスの3つの軸 (`Box.AxisX`, `Box.AxisY`, `Box.AxisZ`) は、点群の共分散行列の固有ベクトルに対応します。これらは、点群の散らばりの主要な方向（主成分）を表します。
-   **広がり (Extents):** ボックスの各軸に沿った広がり (`Box.Extent`) は、共分散行列の固有値に対応します。これらは、各主軸方向への点群の分散（標準偏差の二乗）の尺度となります。
-   **重み付きフィッティング:** 各点に重みを付けてフィッティングを行うコンストラクタも提供されており、点ごとの重要度を変えることができます。

## 結果の解釈

-   `Box.AxisX`: 分散が最も小さい方向（最も点が密集している方向）。
-   `Box.AxisY`: 2番目に分散が小さい方向。
-   `Box.AxisZ`: 分散が最も大きい方向（最も点が広がっている方向）。
-   固有値は昇順でソートされるため、`Box.Extent.x <= Box.Extent.y <= Box.Extent.z` となります。

この結果は、点群の異方性や主要な方向性を分析するのに非常に役立ちます。

## 使い方

### 通常のフィッティング

```csharp
// フィッティング対象の3D点のリスト
List<Vector3d> points = new List<Vector3d>();
points.Add(new Vector3d(1, 1, 0));
points.Add(new Vector3d(2, 1, 0));
points.Add(new Vector3d(3, 1.1, 0.1));
// ... more points

// GaussPointsFit3 のインスタンスを作成して計算を実行
GaussPointsFit3 fit = new GaussPointsFit3(points);

if (fit.ResultValid) {
    // フィットしたボックスを取得
    Box3d resultBox = fit.Box;

    // 結果を使用
    Vector3d center = resultBox.Center;
    Vector3d primaryAxis = resultBox.AxisZ; // 最も広がっている方向
    Vector3d secondaryAxis = resultBox.AxisY;
    Vector3d tertiaryAxis = resultBox.AxisX;  // 最も密集している方向

    double maxSpread = resultBox.Extent.z;
}
```

### 重み付きフィッティング

```csharp
// 点と、それに対応する重みのリスト
List<Vector3d> weightedPoints = ...;
List<double> weights = ...; // weightedPoints と同じ順序・数

// 重み付きでフィッティングを実行
GaussPointsFit3 weightedFit = new GaussPointsFit3(weightedPoints, weights);

if (weightedFit.ResultValid) {
    Box3d resultBox = weightedFit.Box;
    // ...
}
```

## 注意点

-   入力点が少なすぎたり、すべての点が同一平面上や同一直線上にある場合、共分散行列が縮退し、固有値問題がうまく解けない可能性があります。その場合、`ResultValid` は `false` になります。
-   内部では `SymmetricEigenSolver` を使用して固有値と固有ベクトルを計算しています。
