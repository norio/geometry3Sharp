# IntrSegment2Triangle2

## 機能概要

`IntrSegment2Triangle2`クラスは、2次元空間における線分と三角形の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する、または線分で交差する（貫通する）のいずれかが得られます。

## 使い方

### 初期化

`Segment2d` と `Triangle2d` オブジェクトを指定して、`IntrSegment2Triangle2` のインスタンスを作成します。

```csharp
Segment2d segment = new Segment2d(new Vector2d(0.5, -1), new Vector2d(0.5, 1));
Triangle2d triangle = new Triangle2d(new Vector2d(0,0), new Vector2d(2,0), new Vector2d(1,2));

IntrSegment2Triangle2 intersector = new IntrSegment2Triangle2(segment, triangle);
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
- `Quantity`: 交差の数 (1点で交差する場合は1、線分で交差する場合は2)
- `Point0`, `Point1`: 交差する点または線分の端点
- `Param0`, `Param1`: **線分上での**交差パラメータ

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection Type: " + intersector.Type);
    Console.WriteLine("Intersection Quantity: " + intersector.Quantity);

    if (intersector.Quantity == 1)
    {
        Console.WriteLine("Intersection point: " + intersector.Point0);
    }
    else if (intersector.Quantity == 2)
    {
        Console.WriteLine("Intersection segment: [" + intersector.Point0 + ", " + intersector.Point1 + "]");
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
