# MeshAutoRepair

`MeshAutoRepair` クラスは、メッシュの自動修復機能を提供します。複数の修復ステップを組み合わせて、メッシュの一般的な問題を修正します。

## 機能

- **方向の修復:** 連結コンポーネントの面の向きを揃え、グローバルな向きを解決します。
- **クラックの修復:** `MergeCoincidentEdges` を使用して、指定された許容範囲内のクラックを閉じます。
- **縮退エッジの削除:** `MinEdgeLengthTol` より短いエッジを折りたたみます。
- **穴埋め:** 単純な穴（3、4頂点）から、より複雑な穴まで段階的に埋めます。
- **ボウタイの切断:** 頂点で接続されているだけの面（ボウタイ）を切断します。
- **不要な部分の削除:** `RemoveMode` の設定に応じて、内部のジオメトリや隠れた（オクルードされた）ジオメトリを削除します。
- **侵食:** 修復が困難な場合、境界の三角形を削除して再試行します（`ErosionIterations`）。

## 使い方

```csharp
// 修復したいメッシュを取得
DMesh3 mesh = ...;

// MeshAutoRepair のインスタンスを作成
MeshAutoRepair repair = new MeshAutoRepair(mesh);

// 修復の許容誤差を設定 (オプション)
repair.RepairTolerance = 0.001;

// 最小エッジ長を設定 (オプション)
repair.MinEdgeLengthTol = 0.0001;

// 内部のジオメトリを削除するように設定 (オプション)
repair.RemoveMode = MeshAutoRepair.RemoveModes.Interior;

// 修復処理を実行
bool success = repair.Apply();

if (success) {
    // 修復されたメッシュは repair.Mesh に格納されています
    DMesh3 repairedMesh = repair.Mesh;
    // ...
}
```
