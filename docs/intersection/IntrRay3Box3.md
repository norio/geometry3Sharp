# IntrRay3Box3

## 機能概要

`IntrRay3Box3`クラスは、3次元空間におけるレイ（半直線）と有向境界ボックス（Oriented Bounding Box, OBB）の交差を判定するためのクラスです。任意の向きを持つボックスとの交差を計算できます。

交差判定の結果として、交差しない、1点で交差する（接する）、または線分で交差する（貫通する）のいずれかが得られます。

## 使い方

### 初期化

`Ray3d` と `Box3d` オブジェクトを指定して、`IntrRay3Box3` のインスタンスを作成します。

```csharp
Ray3d ray = new Ray3d(new Vector3d(0, 0, 0), new Vector3d(1, 0, 0).Normalized);
Box3d box = new Box3d(new Vector3d(2, 0, 0), Vector3d.AxisX, Vector3d.AxisY, Vector3d.AxisZ, new Vector3d(1,2,3));
// Rotate the box
box.AxisX = new Vector3d(0,1,0);
box.AxisY = new Vector3d(-1,0,0);

IntrRay3Box3 intersector = new IntrRay3Box3(ray, box);
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

インスタンスを生成せずに交差の有無を高速に判定するための静的メソッドが提供されています。

- `Intersects(ref Ray3d ray, ref Box3d box, double expandExtents = 0)`: 交差の有無を判定します。

```csharp
// Test for intersection
bool hit = IntrRay3Box3.Intersects(ref ray, ref box);
```
