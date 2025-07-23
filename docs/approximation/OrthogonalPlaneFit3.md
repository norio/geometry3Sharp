# OrthogonalPlaneFit3

`OrthogonalPlaneFit3` クラスは、3D点群に最もよくフィットする平面を、最小二乗法を用いて計算します。このフィッティングは、各点から平面への直交距離を最小化することに基づいています。

この手法は、主成分分析（PCA）と密接に関連しており、点群の共分散行列を解析することで解かれます。

## 機能

-   **平面の原点 (`Origin`):** フィットした平面が通過する点。これは、入力された点群の平均（重心）になります。
-   **平面の法線 (`Normal`):** フィットした平面の法線ベクトル。これは、点群の共分散行列の最小の固有値に対応する固有ベクトルです。直感的には、これは点群の散らばりが最も小さい方向、つまり点群が最も「平面的」に見える方向を指します。

## 使い方

```csharp
// フィッティング対象の3D点のリスト
List<Vector3d> points = new List<Vector3d>();
points.Add(new Vector3d(1, 1, 0.1));
points.Add(new Vector3d(2, 1, -0.1));
points.Add(new Vector3d(3, 1.1, 0.0));
points.Add(new Vector3d(1, 2, 0.2));
points.Add(new Vector3d(2, 2, 0.0));
points.Add(new Vector3d(3, 2.1, -0.2));
// ... more points that are roughly on a plane

// OrthogonalPlaneFit3 のインスタンスを作成して計算を実行
OrthogonalPlaneFit3 fit = new OrthogonalPlaneFit3(points);

if (fit.ResultValid) {
    // フィットした平面の情報を取得
    Vector3d planeOrigin = fit.Origin;
    Vector3d planeNormal = fit.Normal;

    // Plane3d オブジェクトとして利用
    Plane3d fittedPlane = new Plane3d(planeNormal, planeOrigin);

    // ある点が平面のどちら側にあるかをテスト
    double distance = fittedPlane.DistanceTo(new Vector3d(5, 5, 5));
}
```

## アルゴリズムの概要

1.  **平均の計算:** 入力されたすべての点の平均（重心）を計算し、これを平面の `Origin` とします。
2.  **共分散行列の計算:** 各点を（重心からの相対ベクトルとして）扱い、これらのベクトルの共分散行列を計算します。
3.  **固有値問題の解決:** `SymmetricEigenSolver` を使用して、共分散行列の固有値と固有ベクトルを求めます。
4.  **法線の決定:** 最小の固有値に対応する固有ベクトルが、平面の法線ベクトル `Normal` となります。これは、点群の分散が最も小さい方向を表します。

## 注意点

-   入力点が少なすぎたり、すべての点が同一直線上にある場合、共分散行列が縮退し、一意の平面を決定できない可能性があります。その場合、`ResultValid` が `false` になることがあります。
-   このクラスは、点群に重みを付ける機能は提供していません。すべての点が等しく扱われます。
