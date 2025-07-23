# MergeCoincidentEdges

`MergeCoincidentEdges` クラスは、メッシュ内の同一頂点を共有する境界エッジ（Coincident Edges）をマージする機能を提供します。これにより、メッシュの「クラック」や不要な境界を閉じることができます。

## 機能

- `MergeDistance` プロパティで指定された距離内にある頂点を持つエッジを同一と見なします。
- `OnlyUniquePairs` プロパティを `true` に設定すると、相互に唯一のペアであるエッジのみをマージします。
- `Apply` メソッドを実行すると、メッシュ内の重複する境界エッジを探索し、マージします。

## 使い方

```csharp
// メッシュを取得
DMesh3 mesh = ...;

// MergeCoincidentEdges のインスタンスを作成
MergeCoincidentEdges merger = new MergeCoincidentEdges(mesh);

// マージ距離を設定 (オプション)
merger.MergeDistance = 0.001;

// ユニークなペアのみをマージする場合 (オプション)
// merger.OnlyUniquePairs = true;

// マージ処理を実行
bool success = merger.Apply();

if (success) {
    // メッシュが更新されました
}
```
