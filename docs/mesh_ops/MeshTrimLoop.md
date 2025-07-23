# MeshTrimLoop

`MeshTrimLoop` クラスは、サーフェス上またはその近くにある3Dカーブ（`DCurve3`）を使用してメッシュをトリミング（切断・削除）します。

## 機能

1.  **内側領域の特定:** `MeshFacesFromLoop` を使用して、`TrimLine` で囲まれたメッシュの領域を特定します。この際、`seed_tri` または `seed_pt` を開始点として使用し、カーブの内側にある三角形を洪水塗り（flood-fill）のように選択します。
2.  **三角形の削除:** 特定された領域の三角形をメッシュから削除します。
3.  **クリーンアップ:** 削除後に残った可能性のある小さな連結コンポーネントやボウタイ頂点（bowtie vertices）を削除し、最大の連結コンポーネントのみを残します。
4.  **境界の再メッシュ化:** 新しく生成された境界ループの周辺領域 (`RemeshBorderRings` でリング数を指定) を `RegionRemesher` を使用して再メッシュ化します。
5.  **境界の適合:** 再メッシュ化の際、新しい境界ループの頂点を、元の `TrimLine` カーブと元のメッシュサーフェス（`Spatial`）の両方に射影して拘束します。これにより、トリム後の境界が滑らかになり、元のカーブ形状に適合します。

## 使い方

```csharp
// トリミングしたいメッシュ
DMesh3 mesh = ...;
// トリミングに使用する3Dカーブ
DCurve3 trimCurve = ...;
// 元のメッシュサーフェスを保持する空間データ構造（AABBTree）
// 注意：これは 'mesh' のコピーである必要があります。
DMeshAABBTree3 spatial = new DMeshAABBTree3(new DMesh3(mesh), true);

// カーブの内側にあることがわかっているシード三角形のID
int seedTriangleId = ...;

// MeshTrimLoop のインスタンスを作成
MeshTrimLoop trimmer = new MeshTrimLoop(mesh, trimCurve, seedTriangleId, spatial);

// パラメータを設定 (オプション)
trimmer.RemeshBorderRings = 3;      // 境界から3リングを再メッシュ化
trimmer.TargetEdgeLength = 5.0;     // 再メッシュ化の目標エッジ長
trimmer.RemeshRounds = 30;          // 再メッシュ化の反復回数

// トリミング処理を実行
bool success = trimmer.Trim();

if (success) {
    // トリミングが成功しました
    // メッシュは直接更新されています
}
```

## 注意点

-   **`Spatial` の要件:** このクラスは、元の（トリミング前の）サーフェス形状に境界を再投影するために、`DMeshAABBTree3` 形式の空間データ構造を必要とします。**重要な点として、この `DMeshAABBTree3` が保持するメッシュは、操作対象の `Mesh` のコピーでなければなりません。** なぜなら、操作対象の `Mesh` からは三角形が削除されるため、再投影のターゲットとして使えなくなるからです。コンストラクタで `null` を渡すと、内部で自動的にコピーが作成されます。
-   **シード点:** `TrimLine` のどちら側を削除するかを指定するために、シード（`seed_tri` または `seed_pt`）が必要です。これは、削除したい領域の内側にある必要があります。
-   **既存の穴:** 現在の実装では、入力メッシュに既存の穴がある場合の処理は完全にはサポートされていません。トリミングによって複数の境界ループが生成されると、処理が失敗する可能性があります。
