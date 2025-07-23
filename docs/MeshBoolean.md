# MeshBoolean

`MeshBoolean` クラスは、2つのメッシュ間のブーリアン演算（和、差、積）を行うための準備段階の機能を提供します。（現在の実装は、2つのメッシュを相互にカットし、結果を結合するまでです。完全なブーリアン演算は未実装です。）

## 機能

- `Target` メッシュと `Tool` メッシュを入力として受け取ります。
- `MeshMeshCut` を使用して、`Target` を `Tool` で、`Tool` を `Target` でそれぞれカットします。
- カット操作中に、`VertexSnapTol` で指定された許容誤差内で頂点をスナップします。
- カット後、`RemoveContained` を呼び出して、完全に内側に含まれる部分を削除します。
- `Compute` メソッドを実行すると、両方のメッシュをカットし、その結果を `Result` メッシュに結合します。

## 使い方

```csharp
// ターゲットメッシュとツールメッシュを取得
DMesh3 targetMesh = ...;
DMesh3 toolMesh = ...;

// MeshBoolean のインスタンスを作成
MeshBoolean booleanOp = new MeshBoolean();
booleanOp.Target = targetMesh;
booleanOp.Tool = toolMesh;

// 頂点スナップの許容誤差を設定 (オプション)
booleanOp.VertexSnapTol = 0.0001;

// ブーリアン演算（の準備段階）を実行
bool success = booleanOp.Compute();

if (success) {
    // 結果のメッシュを取得
    DMesh3 resultMesh = booleanOp.Result;
    // ...
}
```

**注意:** このクラスは現在、完全なブーリアン演算（Union, Difference, Intersection）を実装していません。`Compute` の結果は、2つのメッシュを相互にカットし、単純に結合したメッシュになります。完全なブーリアン演算を行うには、この結果に対してさらなる処理が必要です。
