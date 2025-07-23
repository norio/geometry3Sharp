# QuadraticFit2

`QuadraticFit2` クラスは、2D点群に二次曲線（円錐曲線）をフィッティングするための静的メソッドを提供します。

## `Fit` メソッド

### 機能

`Fit` メソッドは、与えられた2D点群に最もよくフィットする一般的な二次曲線を最小二乗法で計算します。

フィットする二次曲線の方程式は次の形式です。
`C[0] + C[1]*X + C[2]*Y + C[3]*X^2 + C[4]*Y^2 + C[5]*X*Y = 0`

このメソッドは、係数ベクトル `C` の長さが1であるという制約の下で、誤差 `E(C) = C^t * M * C` を最小化します。ここで `M` は点群から計算されるモーメント行列です。この問題は、行列 `M` の最小の固有値とそれに対応する固有ベクトルを見つけることに帰着します。

-   **出力:**
    -   `coefficients` (double[6]): 計算された二次曲線の係数 `C[0]` から `C[5]`。これは `M` の最小固有値に対応する固有ベクトルです。
    -   `return` (double): フィットの誤差の尺度となる最小固有値。0に近いほどフィットが良いことを示します。

### 使い方

```csharp
// フィッティング対象の2D点の配列
Vector2d[] points = new Vector2d[] {
    new Vector2d(0, 1),
    new Vector2d(1, 0),
    new Vector2d(0, -1),
    new Vector2d(-1, 0),
    new Vector2d(0.707, 0.707)
    // ... more points on an ellipse or other quadratic curve
};

// 係数を格納する配列
double[] coefficients = new double[6];

// フィッティングを実行
double fitError = QuadraticFit2.Fit(points, coefficients);

Console.WriteLine($"Fit Error: {fitError}");
Console.WriteLine("Coefficients:");
for (int i = 0; i < 6; ++i) {
    Console.WriteLine($"  C[{i}]: {coefficients[i]}");
}
```

## `FitCircle2` メソッド

### 機能

`FitCircle2` メソッドは、点群がほぼ円形であると想定される場合に、特別に円をフィッティングします。

これは、二次方程式の項を `X^2 + Y^2` に制約することで、よりロバストな円のフィッティングを行います。

-   **出力:**
    -   `circle` (out Circle2d): フィットした円の `Circle2d` オブジェクト。中心と半径が含まれます。
    -   `return` (double): フィットの誤差の尺度となる最小固有値。

### 使い方

```csharp
// ほぼ円上にある2D点の配列
Vector2d[] circlePoints = ...;

Circle2d fittedCircle;

// 円のフィッティングを実行
double circleFitError = QuadraticFit2.FitCircle2(circlePoints, out fittedCircle);

Console.WriteLine($"Circle Fit Error: {circleFitError}");
Console.WriteLine($"Fitted Circle: Center={fittedCircle.Center}, Radius={fittedCircle.Radius}");
```

## 注意点

-   これらのメソッドは、内部で `SymmetricEigenSolver` を使用して固有値問題を解いています。
-   `Fit` メソッドは一般的な二次曲線（楕円、放物線、双曲線）をフィッティングできますが、点群が特定の形状に偏っている場合、結果が不安定になる可能性があります。
-   点群が円に近いことがわかっている場合は、`FitCircle2` を使用する方が、より安定して正確な結果が得られます。
-   数値的な丸め誤差により、完全なフィットの場合でも戻り値（誤差）がわずかに負になる可能性があるため、メソッドは絶対値を返します。
