# IntrLine3AxisAlignedBox3

## 機能概要

`IntrLine3AxisAlignedBox3`クラスは、3次元空間における線（無限に続く直線）と軸並行境界ボックス（Axis-Aligned Bounding Box, AABB）の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する（接する）、または線分で交差する（貫通する）のいずれかが得られます。

## 使い方

### 初期化

`Line3d` と `AxisAlignedBox3d` オブジェクトを指定して、`IntrLine3AxisAlignedBox3` のインスタンスを作成します。

```csharp
Line3d line = new Line3d(new Vector3d(0, 0, 0), new Vector3d(1, 1, 1).Normalized);
AxisAlignedBox3d box = new AxisAlignedBox3d(new Vector3d(-1, -1, -1), new Vector3d(1, 1, 1));

IntrLine3AxisAlignedBox3 intersector = new IntrLine3AxisAlignedBox3(line, box);
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
- `LineParam0`, `LineParam1`: 線上における交差点までのパラメータ

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
