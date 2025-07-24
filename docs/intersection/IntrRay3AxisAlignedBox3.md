# IntrRay3AxisAlignedBox3

## 機能概要

`IntrRay3AxisAlignedBox3`クラスは、3次元空間におけるレイ（半直線）と軸並行境界ボックス（Axis-Aligned Bounding Box, AABB）の交差を判定するためのクラスです。

交差判定の結果として、交差しない、1点で交差する（接する）、または線分で交差する（貫通する）のいずれかが得られます。レイは始点から一方向に無限に伸びるため、交差は最大でも1つの線分になります。

## 使い方

### 初期化

`Ray3d` と `AxisAlignedBox3d` オブジェクトを指定して、`IntrRay3AxisAlignedBox3` のインスタンスを作成します。

```csharp
Ray3d ray = new Ray3d(new Vector3d(0, 0, 0), new Vector3d(1, 1, 1).Normalized);
AxisAlignedBox3d box = new AxisAlignedBox3d(new Vector3d(1, 1, 1), 1.0);

IntrRay3AxisAlignedBox3 intersector = new IntrRay3AxisAlignedBox3(ray, box);
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
- `RayParam0`, `RayParam1`: レイ上における交差点までのパラメータ

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection Type: " + intersector.Type);
    if (intersector.Quantity == 1) {
        Console.WriteLine("Intersection at t=" + intersector.RayParam0 + ", point=" + intersector.Point0);
    } else if (intersector.Quantity == 2) {
        Console.WriteLine("Intersection segment from t=" + intersector.RayParam0 + " to " + intersector.RayParam1);
    }
}
else
{
    Console.WriteLine("No intersection.");
}
```

## 静的メソッド

インスタンスを生成せずに交差判定を行うための静的メソッドも提供されています。

- `Intersects(ref Ray3d ray, ref AxisAlignedBox3d box, double expandExtents = 0)`: 高速に交差の有無を判定します。
- `FindRayIntersectT(ref Ray3d ray, ref AxisAlignedBox3d box, out double RayParam)`: 最初の交差点までのレイのパラメータ `t` を返します。交差しない場合は `false` を返します。

```csharp
// Test for intersection
bool hit = IntrRay3AxisAlignedBox3.Intersects(ref ray, ref box);

// Get T-value of first hit
double rayT;
if (IntrRay3AxisAlignedBox3.FindRayIntersectT(ref ray, ref box, out rayT)) {
    Vector3d hitPoint = ray.PointAt(rayT);
}
```
