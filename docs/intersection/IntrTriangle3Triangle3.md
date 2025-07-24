# IntrTriangle3Triangle3

## 機能概要

`IntrTriangle3Triangle3`クラスは、3次元空間における2つの三角形の交差を判定するためのクラスです。

交差判定の結果は、交差しない、1点で交差する、線分で交差する、または（同一平面上の場合）ポリゴンで交差する、のいずれかになります。3D空間で2つの三角形が交差する場合、その交差は通常1本の線分になります。

## 使い方

### 初期化

2つの `Triangle3d` オブジェクトを指定して、`IntrTriangle3Triangle3` のインスタンスを作成します。

```csharp
Triangle3d tri1 = new Triangle3d(new Vector3d(0,0,0), new Vector3d(2,0,0), new Vector3d(1,2,0));
Triangle3d tri2 = new Triangle3d(new Vector3d(0,1,-1), new Vector3d(2,1,-1), new Vector3d(1,-1,1));

IntrTriangle3Triangle3 intersector = new IntrTriangle3Triangle3(tri1, tri2);
```

### 交差判定

- **`Test()`**: 高速な分離軸判定（SAT）に基づき、交差の有無のみをブール値で返します。詳細な交差情報は計算しません。
- **`Find()`**: 詳細な交差計算を実行し、交差の種類や交差位置を特定します。

```csharp
// 高速な交差テスト
bool maybeIntersects = intersector.Test();

// 詳細な交差情報の計算
bool hasIntersection = intersector.Find();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `Result`: 交差の結果 (`IntersectionResult.Intersects` など)
- `Type`: 交差のタイプ (`IntersectionType.Point`, `IntersectionType.Segment`, `IntersectionType.Polygon`, `IntersectionType.Empty`)
- `Quantity`: 交差を構成する点の数 (Point: 1, Segment: 2, Polygon: 3以上)
- `Points`: 点または線分で交差する場合の交差点を格納します (`Vector3dTuple3`)。
- `PolygonPoints`: ポリゴンで交差する場合の頂点配列。

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection Type: " + intersector.Type);
    if (intersector.Type == IntersectionType.Point) {
        Console.WriteLine("Intersection Point: " + intersector.Points[0]);
    } else if (intersector.Type == IntersectionType.Segment) {
        Console.WriteLine("Intersection Segment: [" + intersector.Points[0] + ", " + intersector.Points[1] + "]");
    } else if (intersector.Type == IntersectionType.Polygon) {
        Console.WriteLine("Intersection polygon has " + intersector.Quantity + " vertices.");
        for (int i = 0; i < intersector.Quantity; ++i) {
            Console.WriteLine("Vertex " + i + ": " + intersector.PolygonPoints[i]);
        }
    }
}
else
{
    Console.WriteLine("No intersection.");
}
```

### 同一平面上の交差

デフォルトでは、2つの三角形が同一平面上にある場合の交差（完全に含まれる場合など）は検出されません。これを有効にするには、`ReportCoplanarIntersection` フラグを `true` に設定します。この処理は追加のコストがかかります。

```csharp
intersector.ReportCoplanarIntersection = true;
intersector.Find(); // これで同一平面上の交差も検出される
```

## 静的メソッド

インスタンスを生成せずに高速な交差テストを行うための静的メソッドも提供されています。

- `Intersects(ref Triangle3d triangle0, ref Triangle3d triangle1)`: `Test()` と同様の高速な交差判定を行います。

```csharp
if (IntrTriangle3Triangle3.Intersects(ref tri1, ref tri2))
{
    // 交差の可能性あり
}
```
