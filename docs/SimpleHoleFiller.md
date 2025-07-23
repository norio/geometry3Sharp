# SimpleHoleFiller

`SimpleHoleFiller` クラスは、メッシュの穴（`EdgeLoop` で定義される）を埋めるための、最も基本的で高速な方法を提供します。

## 機能

このクラスは、穴の頂点数に応じて2つの方法で穴を埋めます。

1.  **3頂点の穴:** 穴が3つの頂点で構成されている場合、単純に1つの三角形を追加して穴を閉じます。

2.  **4頂点以上の穴:**
    -   穴を構成するすべての頂点の重心（セントロイド）を計算します。
    -   重心の位置に新しい頂点を1つ追加します。
    -   新しく追加した重心の頂点と、穴の境界をなす各エッジから、三角形のファン（扇状の三角形群）を生成して穴を埋めます。

## 使い方

```csharp
// 穴を埋めたいメッシュと、その境界ループを取得
DMesh3 mesh = ...;
EdgeLoop boundaryLoop = ...;

// SimpleHoleFiller のインスタンスを作成
SimpleHoleFiller filler = new SimpleHoleFiller(mesh, boundaryLoop);

// (オプション) 新しく生成する三角形にグループIDを割り当てる
int newGroupID = mesh.AllocateTriangleGroup();

// 穴埋め処理を実行
bool success = filler.Fill(newGroupID);

if (success) {
    // 穴埋めが成功しました
    // メッシュは直接更新されています

    // (オプション) 生成された情報を取得
    int[] newTriangles = filler.NewTriangles;
    if (filler.NewVertex != DMesh3.InvalidID) {
        int centerVertex = filler.NewVertex;
        // ...
    }
} else {
    // 穴埋めに失敗した場合
    // (例: 頂点数が3未満、または三角形の追加に失敗)
}
```

## 注意点

-   この方法は非常に高速ですが、生成されるサーフェスの品質は保証されません。特に、細長い穴や複雑な形状の穴に対しては、品質の低い（細長い）三角形が生成される可能性があります。
-   生成されるサーフェスは、必ずしも周囲のメッシュと滑らかに接続されるわけではありません。
-   より高品質な穴埋めが必要な場合は、`PlanarHoleFiller`、`MinimalHoleFill`、`SmoothedHoleFill` などの、より高度なクラスの使用を検討してください。
-   穴埋めに失敗した場合、追加された可能性のある重心の頂点は自動的に削除され、メッシュは元の状態に戻ります。
