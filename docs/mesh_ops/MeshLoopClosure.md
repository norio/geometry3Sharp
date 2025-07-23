# MeshLoopClosure

`MeshLoopClosure` クラスは、メッシュの開いた境界ループを閉じるための高レベルな操作を提供します。複数のステップを組み合わせて、自然な形でループを閉じようと試みます。

現在の実装 `Close_Flat()` は、ループを指定された平面に押し出し、その平面上で穴を埋め、接続部分を再メッシュ化することで、平坦な「キャップ」を作成します。

## 機能

1. **境界のクリーンアップ:** `cleanup_boundary` を呼び出して、ターゲットループ周辺のトポロジを再メッシュ化し、よりきれいで均一なエッジ長にします。
2. **ループの押し出し:** クリーンアップされたループを `FlatClosePlane` で指定された平面に `MeshExtrudeLoop` を使って押し出します。
3. **ループの平滑化:** 押し出された新しいループを `MeshLoopSmooth` を使って平滑化し、平面上でのジグザグを減らします。
4. **穴埋め:** `SimpleHoleFiller` を使って、平滑化されたループの内側を三角形で埋めます。
5. **再メッシュ化:** 押し出しと穴埋めで生成された領域とその周辺を `RegionRemesher` を使って再メッシュ化し、滑らかで均一なサーフェスに仕上げます。
6. **最終的な平滑化:** 接続領域周辺を `smooth_region` で再度平滑化し、アーティファクトを減らします。

## 使い方

```csharp
// メッシュと閉じたい境界ループを取得
DMesh3 mesh = ...;
EdgeLoop boundaryLoop = ...; // メッシュの有効な境界ループ

// MeshLoopClosure のインスタンスを作成
MeshLoopClosure closure = new MeshLoopClosure(mesh, boundaryLoop);

// キャップを作成する平面を定義
// (例: ループのバウンディングボックスの中心を通り、Y軸に垂直な平面)
Vector3d center = boundaryLoop.GetBounds().Center;
closure.FlatClosePlane = new Frame3f((Vector3f)center, Vector3f.AxisY);

// 目標エッジ長を設定 (0以下の場合、メッシュの平均エッジ長が使用される)
closure.TargetEdgeLen = 0;

// グループIDを割り当てる (オプション)
// closure.ExtrudeGroup = 1;
// closure.FillGroup = 2;

// ループを閉じる処理を実行
bool success = closure.Close();

if (success) {
    // 処理が成功しました
    // メッシュは直接更新されています
}
```

## 静的ユーティリティメソッド

このクラスには、他の場所でも役立つ可能性のある静的メソッドが含まれています。
- `smooth_region`: 指定された頂点の周辺（Nリング）を平滑化します。
- `smooth_loop`: ループ自体とその周辺領域を協調して平滑化します。
- `cleanup_boundary`: 境界ループ周辺を局所的に再メッシュ化してクリーンアップします。
