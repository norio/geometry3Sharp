# BiArcFit2

`BiArcFit2` クラスは、2つの2D点とその点における接線ベクトルを指定して、それらを滑らかに補間する2つの円弧（バイアーク）を計算します。

この実装は、Ryan Juckett の記事 "Biarc Interpolation" に基づいています。バイアークは、CADやグラフィックスで曲線を表現するための一般的で効率的な方法です。

## 機能

-   **基本的なバイアークフィッティング:** 2つの点と2つの接線を指定すると、中間点を共有する2つの円弧（または直線）を計算します。デフォルトでは、2つの円弧の「重み」（`d1` と `d2`）が等しくなるように計算されます。
-   **高度なフィッティング:** `d1` パラメータを明示的に指定することで、2つの円弧の長さの比率を調整し、フィットの形状を制御することができます。
-   **直線への縮退:** 円弧が直線に縮退する特殊なケース（曲率がゼロになる場合）を正しく処理し、結果を `Segment2d` として返します。
-   **結果へのアクセス:**
    -   `Arc1`, `Arc2`: 計算された2つの円弧 (`Arc2d`)。
    -   `Segment1`, `Segment2`: 円弧が直線の場合のセグメント (`Segment2d`)。
    -   `Arc1IsSegment`, `Arc2IsSegment`: 結果が円弧か直線かを判定するフラグ。
    -   `Curve1`, `Curve2`: 結果を `IParametricCurve2d` インターフェース経由で統一的に扱えます。

## 使い方

### 基本的な使い方

```csharp
// 始点、終点、およびそれぞれの接線ベクトルを定義
Vector2d p1 = new Vector2d(0, 0);
Vector2d t1 = Vector2d.AxisX; // (1, 0)
Vector2d p2 = new Vector2d(10, 5);
Vector2d t2 = new Vector2d(1, 1).Normalized;

// BiArcFit2 のインスタンスを作成して計算を実行
BiArcFit2 biarcFit = new BiArcFit2(p1, t1, p2, t2);

// 結果を取得
IParametricCurve2d curve1 = biarcFit.Curve1;
IParametricCurve2d curve2 = biarcFit.Curve2;

// カーブ上の点をサンプリング
Vector2d pointOnCurve1 = curve1.SampleT(0.5);
Vector2d pointOnCurve2 = curve2.SampleT(0.5);

// 中間点（接続点）
Vector2d jointPoint = curve1.SampleT(1.0);
```

### `d1` を指定した高度な使い方

```csharp
// 基本的なフィットをまず計算して、d1 の基準値を得る
BiArcFit2 initialFit = new BiArcFit2(p1, t1, p2, t2);
double base_d1 = initialFit.FitD1;

// d1 の値を調整して、新しいフィットを計算
// (経験的に、[0, 2*base_d1] の範囲が有効なことが多い)
double custom_d1 = base_d1 * 0.5;
BiArcFit2 customFit = new BiArcFit2(p1, t1, p2, t2, custom_d1);

// 結果のカーブを取得
IParametricCurve2d customCurve1 = customFit.Curve1;
IParametricCurve2d customCurve2 = customFit.Curve2;
```

## 注意点

-   入力される接線ベクトルは、正規化（長さが1）されている必要があります。
-   `d1` を指定するコンストラクタは、より柔軟な形状を生成できますが、`d1` の値によっては不適切な結果（非常に大きな円弧など）が生成される可能性もあります。
-   `Epsilon` プロパティは、計算中のゼロ判定に使用される許容誤差です。必要に応じて調整できます。
