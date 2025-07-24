# Intersector1

## 機能概要

`Intersector1`クラスは、1次元の二つの区間 `[u0, u1]` と `[v0, v1]` の交差を判定するためのクラスです。各区間の端点は `u0 <= u1` および `v0 <= v1` のように昇順でなければなりません。

交差判定の結果として、交差がない、点が1つで接する、区間で交差する（点が2つ）のいずれかが得られます。

## 使い方

### 初期化

2つの区間を指定して、`Intersector1` のインスタンスを作成します。

```csharp
// 2つの区間 [0, 5] と [3, 8] を定義
Intersector1 intersector = new Intersector1(0, 5, 3, 8);
```

### 交差判定

`Find()` メソッドを呼び出して交差計算を実行します。戻り値は交差があるかどうか (true/false) です。

```csharp
bool hasIntersection = intersector.Find();
```

### 結果の取得

`Find()` の実行後、以下のプロパティから結果を取得できます。

- `NumIntersections`: 交差の数 (0, 1, または 2)
- `Result`: 交差の結果のタイプ
- `GetIntersection(i)`: 交差している点（iは0または1）

```csharp
if (hasIntersection)
{
    Console.WriteLine("Intersection count: " + intersector.NumIntersections);

    if (intersector.NumIntersections == 1)
    {
        Console.WriteLine("Intersection point: " + intersector.GetIntersection(0));
    }
    else if (intersector.NumIntersections == 2)
    {
        Console.WriteLine("Intersection interval: [" + intersector.GetIntersection(0) + ", " + intersector.GetIntersection(1) + "]");
    }
}
else
{
    Console.WriteLine("No intersection.");
}
```

`Test` プロパティを使えば、`Find()` を呼び出さずに単純なオーバーラップの有無を手軽に確認することもできます。

```csharp
if (intersector.Test)
{
    // 交差または接している
}
```
