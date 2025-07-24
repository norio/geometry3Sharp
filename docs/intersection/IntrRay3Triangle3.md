# IntrRay3Triangle3

## 機能概要

`IntrRay3Triangle3`クラスは、3次元空間におけるレイ（半直線）と三角形の交差を判定するためのクラスです。いわゆるMöller–Trumbore intersection algorithmを実装したもので、レイトレーシングなどのアプリケーションで広く利用されます。

交差は1点で発生し、その結果としてレイ上のパラメータ（距離）と、三角形の重心座標が得られます。

## 使い方

### 初期化

`Ray3d` と `Triangle3d` オブジェクトを指定して、`IntrRay3Triangle3` のインスタンスを作成します。

```csharp
Ray3d ray = new Ray3d(new Vector3d(0.5, 0.5, -1), Vector3d.AxisZ);
Triangle3d triangle = new Triangle3d(new Vector3d(0,0,0), new Vector3d(2,0,0), new Vector3d(1,2,0));

IntrRay3Triangle3 intersector = new IntrRay3Triangle3(ray, triangle);
```

### 交差判定

`Compute()` または `Find()` メソッドを呼び出して交差計算を実行します。

```csharp
bool hasIntersection = intersector.Find();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `Result`: 交差の結果 (`IntersectionResult.Intersects` または `NoIntersection`)
- `Type`: 交差のタイプ (`IntersectionType.Point` または `Empty`)
- `Quantity`: 交差の数 (1 または 0)
- `RayParameter`: レイの始点から交差点までの距離 `t`
- `TriangleBaryCoords`: 交差点の三角形上の重心座標 (u, v, w)

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection found!");

    // 交差点の座標
    Vector3d hitPoint = ray.PointAt(intersector.RayParameter);

    Console.WriteLine("Intersection point: " + hitPoint);
    Console.WriteLine("Ray parameter t: " + intersector.RayParameter);
    Console.WriteLine("Barycentric coords: " + intersector.TriangleBaryCoords);
}
else
{
    Console.WriteLine("No intersection.");
}
```

## 静的メソッド

インスタンスを生成せずに交差判定を行うための静的メソッドも提供されています。

- `Intersects(ref Ray3d ray, ref Vector3d V0, ref Vector3d V1, ref Vector3d V2, out double rayT)`: 交差を判定し、交差する場合はレイのパラメータ `t` を返します。

```csharp
double rayT;
if (IntrRay3Triangle3.Intersects(ref ray, ref triangle.V0, ref triangle.V1, ref triangle.V2, out rayT))
{
    Vector3d hitPoint = ray.PointAt(rayT);
    // 交差あり
}
```
