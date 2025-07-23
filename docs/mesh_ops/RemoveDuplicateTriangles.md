# RemoveDuplicateTriangles

`RemoveDuplicateTriangles` クラスは、メッシュ内に存在する重複した三角形を検出して削除します。

## 機能

1.  **重心ハッシュ:** `PointSetHashtable` を使用して、すべての三角形の重心を空間ハッシュに格納します。これにより、重複の可能性がある三角形のペアを効率的に見つけ出します。
2.  **候補の絞り込み:** 各三角形について、その重心の近く（`VertexTolerance` の範囲内）に重心を持つ他の三角形を候補としてリストアップします。
3.  **頂点の一致確認:** 候補となった三角形ペアの3つの頂点が、`VertexTolerance` の範囲内で一致するかどうかをチェックします。頂点の順序（a,b,c と x,y,z または x,z,y など）も考慮されます。
4.  **向きの確認:** `CheckOrientation` が `true` (デフォルト) の場合、重複していると判断された三角形の法線の向きがほぼ同じであることも確認します。これにより、同じ位置にあるが向きが反対の三角形（いわゆる「裏ポリ」）を誤って削除するのを防ぎます。
5.  **削除:** 重複していると判断された三角形を `Mesh.RemoveTriangle` を使って削除します。

## 使い方

```csharp
// 重複の可能性があるメッシュ
DMesh3 mesh = ...;

// RemoveDuplicateTriangles のインスタンスを作成
RemoveDuplicateTriangles remover = new RemoveDuplicateTriangles(mesh);

// (オプション) 頂点を同一と見なす許容誤差を設定
remover.VertexTolerance = 1e-6;

// (オプション) 法線の向きチェックを無効にする場合
// remover.CheckOrientation = false;

// 重複削除処理を実行
bool success = remover.Apply();

if (success) {
    // 処理が完了しました
    // メッシュは直接更新されています
    int numRemoved = remover.Removed;
    Console.WriteLine($"Removed {numRemoved} duplicate triangles.");
}
```

## 注意点

-   このクラスは、メッシュを直接変更します。元のメッシュを保持したい場合は、事前にコピーを作成してください。
-   `VertexTolerance` の値は、モデルのスケールに応じて適切に設定する必要があります。小さすぎると重複を見逃し、大きすぎると異なる三角形を誤って同一と見なす可能性があります。
-   `CheckOrientation` を `false` にすると、向きが反対の重複三角形も削除される可能性があります。これは通常望ましくない挙動ですが、特定のケースでは有用かもしれません。
