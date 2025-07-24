# IntrTriangle2Triangle2

## 機能概要

`IntrTriangle2Triangle2`クラスは、2次元空間における2つの三角形の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する、線分で交差する、またはポリゴン（凸多角形）で交差する、のいずれかが得られます。

## 使い方

### 初期化

2つの `Triangle2d` オブジェクトを指定して、`IntrTriangle2Triangle2` のインスタンスを作成します。

```csharp
Triangle2d tri1 = new Triangle2d(new Vector2d(0,0), new Vector2d(3,0), new Vector2d(1.5, 3));
Triangle2d tri2 = new Triangle2d(new Vector2d(0,1), new Vector2d(3,1), new Vector2d(1.5, -2));

IntrTriangle2Triangle2 intersector = new IntrTriangle2Triangle2(tri1, tri2);
```

### 交差判定

`Compute()` または `Find()` メソッドを呼び出して、詳細な交差形状を計算します。

```csharp
bool hasIntersection = intersector.Find();
```

`Test()` メソッドを使えば、`Find()` よりも高速に交差の有無のみを判定できます（分離軸判定）。

```csharp
bool maybeIntersects = intersector.Test();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `Result`: 交差の結果 (`IntersectionResult.Intersects` など)
- `Type`: 交差のタイプ (`IntersectionType.Point`, `IntersectionType.Segment`, `IntersectionType.Polygon`, `IntersectionType.Empty`)
- `Quantity`: 交差領域を構成する頂点の数
- `Points`: 交差領域のポリゴンの頂点配列。`Quantity` の数だけ有効な点が格納されています。

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection Type: " + intersector.Type);
    Console.WriteLine("Intersection polygon has " + intersector.Quantity + " vertices.");

    for (int i = 0; i < intersector.Quantity; ++i)
    {
        Console.WriteLine("Vertex " + i + ": " + intersector.Points[i]);
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
