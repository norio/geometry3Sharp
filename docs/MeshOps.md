# SetGroupBehavior

`SetGroupBehavior` 構造体は、メッシュ操作によって新しい三角形が生成される際に、それらにどのグループIDを割り当てるかを制御するための便利な方法を提供します。

多くのメッシュ操作クラス（例: `MeshExtrudeFaces`, `MeshExtrudeMesh`）は、この構造体のインスタンスをプロパティとして持ち、グループの割り当て方法をカスタマイズできるようにしています。

## 機能

`SetGroupBehavior` は3つの主要なモードを持っています。

- **`Ignore`**: グループIDを割り当てません。新しい三角形はデフォルトのグループID（通常は0）を持ちます。
- **`AutoGenerate`**: `mesh.AllocateTriangleGroup()` を呼び出して、新しい一意のグループIDを自動的に生成して割り当てます。
- **`UseConstant`**: 指定された定数のグループIDを割り当てます。

## 使い方

`SetGroupBehavior` は通常、直接インスタンス化するのではなく、静的プロパティやメソッドを通じて作成します。

```csharp
// 例として MeshExtrudeMesh を使用
MeshExtrudeMesh extrudeOp = new MeshExtrudeMesh(mesh);

// 1. グループを割り当てない（デフォルトの挙動）
extrudeOp.OffsetGroup = SetGroupBehavior.Ignore;

// 2. 新しいグループIDを自動生成する
extrudeOp.OffsetGroup = SetGroupBehavior.AutoGenerate;

// 3. 特定のグループID（この場合は 5）を割り当てる
extrudeOp.OffsetGroup = SetGroupBehavior.SetTo(5);


// 操作を実行
extrudeOp.Extrude();

// extrudeOp.OffsetGroupID には、設定に基づいて割り当てられた、
// または生成されたグループIDが格納されます。
int assignedGroupId = extrudeOp.OffsetGroupID;
```

---

# MeshOps

`MeshOps` クラスは、将来的に一般的なメッシュ操作の静的ユーティリティメソッドを格納するために予約されている空のクラスです。現時点では、このクラスに実装されている機能はありません。
