# RegionOperator

`RegionOperator` クラスは、メッシュの特定の領域（三角形のセット）をサブメッシュとして抽出し、そのサブメッシュを編集した後に元のメッシュに再挿入するための便利なラッパーです。

このクラスは、`DSubmesh3` を中心に構築されており、サブメッシュの抽出、編集、再挿入という一連のワークフローを簡素化します。

## 機能

-   **サブメッシュの抽出:** コンストラクタで指定された三角形のセットに基づいて、`DSubmesh3` を自動的に作成し、境界情報などを計算します。
-   **再挿入 (`BackPropagate`):**
    -   元のメッシュから、最初に指定された領域の三角形を削除します。
    -   編集されたサブメッシュを、`MeshEditor.ReinsertSubmesh` を使用して元のメッシュに再挿入します。
    -   頂点と三角形のマッピング情報（`ReinsertSubToBaseMapV`, `ReinsertSubToBaseMapT`）を保持するため、再挿入後もサブメッシュとベースメッシュ間の対応を追跡できます。
-   **頂点位置のみの反映 (`BackPropagateVertices`):**
    -   サブメッシュのトポロジは変更せず、頂点位置のみを元のメッシュに反映させます。これは、サブメッシュ上で平滑化や変形を行った場合に便利です。
-   **非多様体エッジの修復:** `RepairPossibleNonManifoldEdges` メソッドは、再挿入時に非多様体エッジ（3つ以上の面が共有するエッジ）が生成されるのを防ぐために、サブメッシュ内の問題のあるエッジを事前に分割します。`BackPropagate` は内部でこのメソッドを呼び出すことができます。

## 使い方

```csharp
// ベースとなるメッシュと、操作したい領域の三角形IDの配列
DMesh3 baseMesh = ...;
int[] regionTriangles = ...;

// RegionOperator のインスタンスを作成して、サブメッシュを抽出
RegionOperator op = new RegionOperator(baseMesh, regionTriangles);

// 抽出されたサブメッシュを取得
DMesh3 submesh = op.Region.SubMesh;

// --- サブメッシュを編集 ---
// (例: RemesherPro を使って再メッシュ化する)
RemesherPro remesher = new RemesherPro(submesh);
remesher.SetTargetEdgeLength(5.0);
remesher.SmoothSpeedT = 0.5;
MeshConstraintUtil.FixAllBoundaryEdges(remesher);
remesher.FastestRemesh();
// -------------------------

// 編集したサブメッシュを元のメッシュに再挿入
// bAllowSubmeshRepairs=true (デフォルト) で、非多様体エッジの自動修復を試みる
bool success = op.BackPropagate(true);

if (success) {
    // 再挿入が成功しました
    // baseMesh は更新されています

    // 更新された領域の三角形リストを取得
    int[] newRegionTris = op.CurrentBaseTriangles;

    // サブメッシュの頂点がベースメッシュのどの頂点IDに対応するかのマップ
    IndexMap vtxMap = op.ReinsertSubToBaseMapV;
}
```

### 頂点位置のみを反映する場合

```csharp
// (サブメッシュのトポロジは変更せず、頂点位置のみを編集した場合)
//例：LaplacianMeshSmoother を使用
LaplacianMeshSmoother smoother = new LaplacianMeshSmoother(submesh);
// ...制約などを設定...
smoother.SolveAndUpdateMesh();

// 頂点位置の変更をベースメッシュに反映
op.BackPropropagateVertices();
```
