# PlanarSpansFiller

`PlanarSpansFiller` クラスは、平面上にある複数のエッジスパン（`EdgeSpan`）をつなぎ合わせて形成される、単一の多角形の穴を埋める機能を提供します。

このクラスは `PlanarHoleFiller` と似ていますが、入力として `EdgeLoop` ではなく `EdgeSpan` のリストを取る点が異なります。

## 機能

1.  **2Dポリゴンの生成:** 入力された `EdgeSpan` のリストを順番に連結し、単一の2Dポリゴン（`SpansPoly`）を生成します。各頂点は `SetPlane` で指定された平面に投影されます。
2.  **2Dメッシュの生成:** `PlanarHoleFiller` と同様に、一時的な2Dの平面メッシュを生成します。このメッシュの解像度は `FillTargetEdgeLen` によって制御されます。
3.  **2Dポリゴンの挿入:** `MeshInsertUVPolyCurve` を使用して、生成された2Dポリゴンを2Dメッシュにエッジとして挿入（刻印）します。
4.  **2Dメッシュのトリミング:** 挿入後、2Dポリゴンの外側にある2Dメッシュの三角形を削除します。
5.  **3Dへの逆投影と追加:** 完成した2Dのフィルメッシュを元の3D平面に逆投影し、元のメッシュに新しい三角形として追加します。

## 使い方

```csharp
// 穴を埋めたいメッシュ
DMesh3 mesh = ...;
// 穴を構成する、平面上にあるエッジスパンのリスト
List<EdgeSpan> spans = ...;
// スパンが存在する平面
Vector3d planeOrigin = ...;
Vector3d planeNormal = ...;

// PlanarSpansFiller のインスタンスを作成
PlanarSpansFiller filler = new PlanarSpansFiller(mesh, spans);

// スパンが存在する平面を設定
filler.SetPlane(planeOrigin, planeNormal);

// (オプション) フィルメッシュの目標エッジ長を設定
filler.FillTargetEdgeLen = 10.0;

// (重要) 現在、境界のマージは未実装
filler.MergeFillBoundary = false;

// 穴埋め処理を実行
bool success = filler.Fill();

if (success) {
    // 穴埋めが成功しました
    // 新しい三角形がメッシュに追加されていますが、
    // 境界は元のスパンとはマージされていません。
}
```

## 注意点/未実装の機能

-   **境界のマージ (`MergeFillBoundary`):** 現在、この機能は実装されていません。`Fill()` を実行すると、フィルメッシュは新しいコンポーネントとしてメッシュに追加され、元の `EdgeSpan` との間にクラック（隙間）が残ります。このため、`MergeFillBoundary` は `false` に設定する必要があります。
-   **入力:** このクラスは、入力された `EdgeSpan` が連結されて単一の閉じたポリゴンを形成することを前提としています。ネストした穴や複数のポリゴンはサポートしていません。
-   **コネクタ:** スパン間の接続は単純な直線セグメントとして扱われます。
-   **エラー処理:** `MeshInsertUVPolyCurve` での挿入に失敗した場合、`Fill()` は `false` を返しますが、詳細なエラーフィードバックはありません。
