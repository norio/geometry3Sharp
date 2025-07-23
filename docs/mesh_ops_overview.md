# メッシュ操作の概要

このドキュメントでは `mesh_ops/` ディレクトリに含まれる C# クラスの機能と基本的な使い方をまとめます。各クラスは `DMesh3` オブジェクトに対する編集処理を提供します。

## 穴埋め
### `AutoHoleFill`
境界ループを解析して適切なフィラーへ委譲します。`Apply()` を呼び出すだけで穴埋めが行えます。
### `SimpleHoleFiller`
中心から扇状に三角形を張って穴を埋めます。小さな穴向け。`Fill()` を実行。
### `SmoothedHoleFill`
単純な穴埋め後にリメッシュとラプラシアン平滑化を行います。`Apply()`。
### `MinimalHoleFill`
リメッシュしつつエッジ統合と反転を行い曲率を抑えたパッチを作成します。`Apply()`。
### `PlanarHoleFiller`
ループを平面へ射影して三角形分割し、元位置へ戻します。`SetPlane()` 後 `Fill()`。
### `PlanarSpansFiller`
複数の `EdgeSpan` を対象にした平面穴埋め。`Fill()`。
### `MeshLoopClosure`
境界ループを平面まで押し出して隙間を埋めます。`Close()` を実行。

## メッシュ修復
### `MeshAutoRepair`
ひび割れ修正・細辺の折り畳み・穴埋め・向きの統一を連続して行います。`Apply()`。
### `MeshRepairOrientation`
接続成分を同一向きにそろえ、必要ならレイキャストで全体の向きを決定します。`OrientComponents()` と `SolveGlobalOrientation()`。
### `MergeCoincidentEdges`
閾値内で一致する境界エッジを統合します。`MergeDistance` 設定後 `Apply()`。
### `RemoveDuplicateTriangles`
同じ頂点配置を持つ三角形を検出して削除します。`Apply()` 後 `Removed` を確認。
### `RemoveOccludedTriangles`
レイ交差や winding number で内部と判断される三角形を削除します。`InsideMode` 設定後 `Apply()`。

## リメッシュ・変形
### `LaplacianMeshDeformer` と `LaplacianMeshSmoother`
ソフト拘束付きラプラシアン解法でメッシュを変形・平滑化します。`SetConstraint()` 後 `Solve()` か `SolveAndUpdateMesh()`。
### `MeshIterativeSmooth`
頂点集合を繰り返し平滑化します。重みや反復数を設定し `Smooth()`。
### `MeshLoopSmooth`
境界ループを直接平滑化します。`Smooth()` を呼び出します。
### `MeshLocalParam`
局所パラメータ化を計算します。`ComputeToMaxDistance()` で展開し `GetUV()` で取得。
### `MeshIsoCurves`
指定スカラー関数に基づく等値線を生成します。`Compute()` で `DGraph3` を取得。
### `MeshICP`
点群とメッシュの ICP アラインメント。`Solve()` 後 `UpdateVertices()`。

## 切断・ブーリアン
### `MeshPlaneCut`
平面でメッシュを切断し、小辺の統合や穴埋めも行えます。`Cut()` 後 `FillHoles()`。
### `MeshMeshCut`
二つのメッシュを交差させ切断線を埋め込みます。`Compute()` 実行後 `RemoveContained()` も可能。
### `MeshBoolean`
`MeshMeshCut` を利用したブーリアン演算。`Target` と `Tool` を指定し `Compute()`。結果は `Result` に格納。
### `MeshTrimLoop`
3D トリム曲線内部を削除し境界をリメッシュします。`Trim()`。

## 押し出し・ステッチ
### `MeshExtrudeLoop`
境界 `EdgeLoop` を複製して押し出しリング状に接続します。頂点位置は `PositionF` で指定し `Extrude()`。
### `MeshExtrudeFaces`
面集合を外側へ押し出して再接続します。`Extrude()`。
### `MeshExtrudeMesh`
メッシュ全体を複製して元メッシュと縫い合わせます。`Extrude()`。
### `MeshStitchLoops`
頂点数が異なっても二つの境界ループを接続できます。必要なら対応関係を与え `Stitch()`。

## ポリゴン挿入
### `MeshInsertPolygon`
`Polygon2d` をメッシュに挿入します。挿入エッジは `InsertedPolygonEdges`、内部三角形は `InteriorTriangles` に取得。`Insert()` を実行。
### `MeshInsertProjectedPolygon`
3D 曲線やポリゴンを指定平面に投影して挿入します。`ProjectFrame` と `SeedTriangle` を設定し `Insert()`。
### `MeshInsertUVPolyCurve`
UV 空間上のポリラインまたはポリゴンを挿入します。結果の頂点は `CurveVertices` に格納されます。`Apply()`。
### `MeshOps`
三角形グループ ID の設定方法を表す `SetGroupBehavior` 構造体などを含む補助クラスです。

## トポロジー・空間ユーティリティ
### `MeshTopology`
クリースエッジや境界を解析してトポロジ情報を得ます。`Compute()` 後に `Loops` や `Spans` を参照。
### `RegionOperator`
サブメッシュを抽出して編集後に元へ戻す操作を提供します。編集後は `BackPropropagate()` を呼び出します。
### `MeshSpatialSort`
複数メッシュを空間的に分類し固体と空洞に分けます。`AddMesh()` で追加し `Sort()`。
### `MeshAssembly`
メッシュを連結成分に分解し閉じたソリッドと開いたメッシュを分離します。`Decompose()` 実行後 `ClosedSolids` と `OpenMeshes` を確認。
