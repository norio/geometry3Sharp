# IntrLine2Segment2

## 機能概要

`IntrLine2Segment2`クラスは、2次元空間における線（無限に続く直線）と線分の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する、または線分が線上に完全に乗っている、のいずれかが得られます。

## 使い方

### 初期化

`Line2d` と `Segment2d` オブジェクトを指定して、`IntrLine2Segment2` のインスタンスを作成します。

```csharp
Line2d line = new Line2d(new Vector2d(0, 0.5), Vector2d.AxisX);
Segment2d segment = new Segment2d(new Vector2d(0, 0), new Vector2d(1, 1));

IntrLine2Segment2 intersector = new IntrLine2Segment2(line, segment);
```

### 交差判定

`Compute()` または `Find()` メソッドを呼び出して交差計算を実行します。

```csharp
bool hasIntersection = intersector.Find();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `Result`: 交差の結果 (`IntersectionResult.Intersects` など)
- `Type`: 交差のタイプ (`IntersectionType.Point`, `IntersectionType.Segment`, `IntersectionType.Empty`)
- `Quantity`: 交差の数 (1点で交差する場合は1、線分が線上に重なる場合は `int.MaxValue`)
- `Point`: 交差する点（`Type`が`Point`の場合）
- `Parameter`: 線上における交差点までのパラメータ（`Type`が`Point`の場合）

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection Type: " + intersector.Type);

    if (intersector.Type == IntersectionType.Point)
    {
        Console.WriteLine("Intersection point: " + intersector.Point);
    }
    else if (intersector.Type == IntersectionType.Segment)
    {
        Console.WriteLine("Segment is collinear with the line.");
    }
}
else
{
    Console.WriteLine("No intersection.");
}
```

### IsSimpleIntersection

`IsSimpleIntersection` プロパティは、1点で交差しているかどうかを簡単に確認するためのショートカットです。

```csharp
if (intersector.IsSimpleIntersection)
{
    // 1点で交差している
}
```

### 許容誤差

- `IntervalThreshold`: 線分の端点の数値誤差を許容するための閾値。
- `DotThreshold`: 2つの線形要素が平行かどうかを判定する際の数値誤差の閾値。

これらの値を設定することで、浮動小数点数演算に起因する問題を緩和できます。
