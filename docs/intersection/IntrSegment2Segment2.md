# IntrSegment2Segment2

## 機能概要

`IntrSegment2Segment2`クラスは、2次元空間における2つの線分の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する、または線分で重なって交差する（同一線上でオーバーラップ）のいずれかが得られます。

## 使い方

### 初期化

2つの `Segment2d` オブジェクトを指定して、`IntrSegment2Segment2` のインスタンスを作成します。

```csharp
Segment2d seg1 = new Segment2d(new Vector2d(0, 0), new Vector2d(2, 2));
Segment2d seg2 = new Segment2d(new Vector2d(0, 2), new Vector2d(2, 0));

IntrSegment2Segment2 intersector = new IntrSegment2Segment2(seg1, seg2);
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
- `Quantity`: 交差の数 (1点で交差する場合は1、線分で重なる場合は2)
- `Point0`, `Point1`: 交差する点、または重なる線分の端点（`Point1`は`Quantity`が2の場合のみ有効）
- `Parameter0`, `Parameter1`: **segment1上での**交差パラメータ（`Parameter1`は`Quantity`が2の場合のみ有効）

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

### 許容誤差

- `IntervalThreshold`: 線分の端点の数値誤差を許容するための閾値。
- `DotThreshold`: 2つの線分が平行かどうかを判定する際の数値誤差の閾値。

これらの値を設定することで、浮動小数点数演算に起因する問題を緩和できます。
