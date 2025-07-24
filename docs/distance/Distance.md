# Distance

`Distance` クラスは、距離計算に関連する静的ユーティリティメソッドを格納するためのものです。

**注:** 現在このクラスに含まれているメソッドは限定的であり、将来的には削除される可能性があります。

## メソッド

### `ClosestPointOnLineT(Vector3f p0, Vector3f dir, Vector3f pt)`

#### 機能

与えられた点 `pt` を、`p0` を通り `dir` 方向を持つ無限の直線に射影したときの、直線上でのパラメータ `t` を計算します。

この `t` は、直線上の最近接点が `p0 + t * dir` で表されることを意味します。

-   `p0`: 直線上の点。
-   `dir`: 直線の方向ベクトル。**正規化されている必要があります。**
-   `pt`: 射影する点。

#### 戻り値

-   `float`: 直線上の最近接点に対応するパラメータ `t`。

#### 使い方

```csharp
// 直線と点を定義
Vector3f lineOrigin = new Vector3f(10, 0, 0);
Vector3f lineDirection = Vector3f.AxisY; // Y軸方向
Vector3f point = new Vector3f(15, 20, 0);

// 直線上の最近接点のパラメータを計算
float t = Distance.ClosestPointOnLineT(lineOrigin, lineDirection, point);

// t を使って最近接点の座標を計算
Vector3f closestPoint = lineOrigin + t * lineDirection;

Console.WriteLine($"Parameter t: {t}"); // 結果: 20.0
Console.WriteLine($"Closest Point: {closestPoint}"); // 結果: (10, 20, 0)
```
