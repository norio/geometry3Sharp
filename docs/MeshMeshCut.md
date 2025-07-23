# MeshMeshCut

`MeshMeshCut` クラスは、`Target` メッシュを `CutMesh` で切断（カット）する機能を提供します。2つのメッシュが交差する線に沿って `Target` メッシュの三角形を分割し、`CutMesh` の形状を `Target` に刻印します。

## 機能

1. **交差セグメントの検出:** `DMeshAABBTree3` を使用して、`Target` と `CutMesh` の間のすべての交差セグメントを効率的に見つけます。
2. **交差頂点の分類:** 各交差セグメントの端点が、`Target` メッシュの既存の頂点、エッジ、または面のいずれにあるかを分類します。
3. **頂点の挿入:**
    - 面上にある頂点は、`PokeTriangle` を使って挿入します。
    - エッジ上にある頂点は、`SplitEdge` を使って挿入します。
4. **エッジの接続:** すべての交差頂点が `Target` メッシュに挿入された後、それらが交差セグメントに対応するエッジで接続されるようにします。接続されていない場合は、`MeshInsertUVPolyCurve` を使用して、関連する面を平面に投影し、その2D平面上でセグメントを挿入することで接続を確立します。
5. **内側部分の削除:** `RemoveContained()` メソッドを呼び出すと、`CutMesh` の内側に完全に含まれる `Target` メッシュの三角形を削除できます。

## 使い方

```csharp
// ターゲットメッシュとカッターメッシュを用意
DMesh3 targetMesh = ...;
DMesh3 cutMesh = ...;

// MeshMeshCut のインスタンスを作成
MeshMeshCut cutter = new MeshMeshCut();
cutter.Target = targetMesh;
cutter.CutMesh = cutMesh;

// 頂点をスナップする許容誤差を設定 (オプション)
cutter.VertexSnapTol = 1e-5;

// カット処理を実行
cutter.Compute();

// (オプション) CutMesh に含まれる部分を Target から削除
cutter.RemoveContained();

// カット操作後のメッシュは cutter.Target に格納されています
DMesh3 resultMesh = cutter.Target;

// (オプション) カットによって生成された頂点のリストを取得
List<int> cutVertices = cutter.CutVertices;
```

## 注意点

- このクラスは、入力メッシュが閉じた多様体メッシュであることを想定しています。
- `Compute()` を実行すると、`Target` メッシュは直接変更されます。元のメッシュを保持したい場合は、事前にコピーを作成してください。
- `RemoveContained()` は、`CutMesh` が閉じたソリッドである場合に正しく機能します。
- `ParentFaces` と `SubFaces` ディクショナリは、元の三角形がどの新しい三角形に分割されたかを追跡するために内部で使用されます。`ColorFaces()` は、この情報を使ってデバッグ用に面を色分けするヘルパーメソッドです。
