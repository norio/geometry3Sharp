# MeshStitchLoops

`MeshStitchLoops` クラスは、頂点数が異なる可能性のある2つのエッジループ `Loop0` と `Loop1` を三角形のストリップでつなぎ合わせる（スティッチする）機能を提供します。

## 機能

- **非対称なループのスティッチ:** `MeshEditor.StitchLoop` とは異なり、2つのループの頂点数が一致している必要はありません。
- **対応点の指定:** `AddKnownCorrespondences` メソッドを使用して、2つのループ間で対応することがわかっている頂点のペアを指定できます。これにより、スティッチのねじれを防ぎ、より制御された結果を得ることができます。
- **単純なジッパー法:** `stitch_span_simple` メソッドは、2つのループ間をジッパーのように交互に三角形でつなぎ合わせることで、できるだけ多くの四角形（2つの三角形）を生成します。どちらか一方のループの頂点が余った場合は、残りの部分を三角形ファンで閉じます。
- **ループの向きの信頼性:** `TrustLoopOrientations` プロパティを `false` に設定すると、メッシュの既存の境界エッジの向きに基づいて、スティッチする三角形の向きを自動的に決定しようと試みます。

## 使い方

```csharp
// スティッチしたいメッシュと2つの境界ループを取得
DMesh3 mesh = ...;
EdgeLoop loopA = ...;
EdgeLoop loopB = ...;

// MeshStitchLoops のインスタンスを作成
MeshStitchLoops stitcher = new MeshStitchLoops(mesh, loopA, loopB);

// (強く推奨) 対応する頂点を指定
// これにより、loopA の vertA1 は loopB の vertB1 に、
// vertA2 は vertB2 に対応するようにスティッチされます。
int[] correspondingVertsA = new int[] { vertA1, vertA2, ... };
int[] correspondingVertsB = new int[] { vertB1, vertB2, ... };
stitcher.AddKnownCorrespondences(correspondingVertsA, correspondingVertsB);

// (オプション) 新しい三角形にグループIDを割り当てる
// stitcher.Group = SetGroupBehavior.SetTo(5);

// スティッチ処理を実行
bool success = stitcher.Stitch();

if (success) {
    // スティッチが成功しました
    // メッシュは直接更新されています
}
```

## 注意点

- `AddKnownCorrespondences` を呼び出さずに `Stitch()` を実行すると、現在の実装では例外がスローされます。「ブラインド」スティッチはまだサポートされていません。
- 現在の実装 (`stitch_span_simple`) は比較的単純です。2つのループ間の距離や形状を考慮した、より高度なスティッチ方法は実装されていません。
- ループの向きが正しくないと、生成される三角形のストリップが自己交差する可能性があります。`TrustLoopOrientations = false` を試すか、入力ループの向きを事前に確認してください。
