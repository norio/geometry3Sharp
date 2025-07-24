# IntrLine2Line2

## 機能概要

`IntrLine2Line2`クラスは、2次元空間における2つの線（無限に続く直線）の交差を判定するためのクラスです。

交差判定の結果として、交差しない（平行）、1点で交差する、または完全に一致する（同一線）のいずれかが得られます。

## 使い方

### 初期化

2つの `Line2d` オブジェクトを指定して、`IntrLine2Line2` のインスタンスを作成します。

```csharp
Line2d line1 = new Line2d(new Vector2d(0, 0), new Vector2d(1, 1).Normalized);
Line2d line2 = new Line2d(new Vector2d(0, 1), new Vector2d(1, -1).Normalized);

IntrLine2Line2 intersector = new IntrLine2Line2(line1, line2);
```

### 交差判定

`Compute()` または `Find()` メソッドを呼び出して交差計算を実行します。

```csharp
bool hasIntersection = intersector.Find();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `Result`: 交差の結果 (`IntersectionResult.Intersects` など)
- `Type`: 交差のタイプ (`IntersectionType.Point`, `IntersectionType.Line`, `IntersectionType.Empty`)
- `Quantity`: 交差の数 (1つの場合は1、同一線上の場合は `int.MaxValue`)
- `Point`: 交差する点（`Type`が`Point`の場合）
- `Segment1Parameter`, `Segment2Parameter`: 各線における交差点までのパラメータ（`Type`が`Point`の場合）

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection Type: " + intersector.Type);

    if (intersector.Type == IntersectionType.Point)
    {
        Console.WriteLine("Intersection point: " + intersector.Point);
    }
    else if (intersector.Type == IntersectionType.Line)
    {
        Console.WriteLine("Lines are collinear.");
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
