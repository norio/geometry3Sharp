# IntrSegment3Box3

## 機能概要

`IntrSegment3Box3`クラスは、3次元空間における線分と有向境界ボックス（Oriented Bounding Box, OBB）の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する、または線分で交差する（貫通する）のいずれかが得られます。

## 使い方

### 初期化

`Segment3d` と `Box3d` オブジェクトを指定して、`IntrSegment3Box3` のインスタンスを作成します。3つ目の引数 `solidBox` は、線分がボックスに完全に含まれている場合に交差とみなすかどうかを指定します。

- `solidBox = true`: 完全に含まれる線分は交差とみなされます。
- `solidBox = false`: 完全に含まれる線分は交差とみなされません（境界との交差のみ）。

```csharp
Segment3d segment = new Segment3d(new Vector3d(-2, 0, 0), new Vector3d(2, 0, 0));
Box3d box = new Box3d(Vector3d.Zero, Vector3d.One);

// true: 線分が完全にBoxに含まれていても交差とみなす
IntrSegment3Box3 intersector = new IntrSegment3Box3(segment, box, true);
```

### 交差判定

`Compute()` または `Find()` メソッドを呼び出して交差計算を実行します。

```csharp
bool hasIntersection = intersector.Find();
```

`Test()` メソッドを使えば、`Find()` よりも高速に交差の有無のみを判定できます。

```csharp
bool maybeIntersects = intersector.Test();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `Result`: 交差の結果 (`IntersectionResult.Intersects` など)
- `Type`: 交差のタイプ (`IntersectionType.Point`, `IntersectionType.Segment`, `IntersectionType.Empty`)
- `Quantity`: 交差の数 (1点で交差する場合は1、線分で交差する場合は2)
- `Point0`, `Point1`: 交差する点または線分の端点
- `SegmentParam0`, `SegmentParam1`: **線分上での**交差パラメータ

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
