# PlanarHoleFiller

`PlanarHoleFiller` クラスは、メッシュの平面的な穴を埋めるための高度な機能を提供します。特に、穴の中にさらに別の穴があるような、ネストしたトポロジを持つ複雑な穴を処理することができます。

## 機能

1.  **平面への投影:** `SetPlane` で指定された平面に、入力されたすべての穴の境界ループ (`EdgeLoop`) を2Dポリゴンとして投影します。
2.  **トポロジの解決:** `PlanarComplex` を使用して、投影された2Dポリゴン間の包含関係を解決し、1つまたは複数の「ソリッド領域」（外側の境界と、それに含まれる内側の穴のセット）を特定します。
3.  **2Dメッシュの生成:** 各ソリッド領域に対して、一時的な2Dの平面メッシュを生成します。このメッシュの解像度は `FillTargetEdgeLen` によって制御されます。
4.  **2Dポリゴンの挿入:** `MeshInsertUVPolyCurve` を使用して、ソリッド領域を定義する各2Dポリゴン（外側と内側の穴）を、生成された2Dメッシュにエッジとして挿入（刻印）します。
5.  **2Dメッシュのトリミング:** 挿入後、ソリッド領域の外側にある2Dメッシュの三角形を削除します。
6.  **3Dへの逆投影と結合:** 完成した2Dのフィルメッシュを元の3D平面に逆投影し、元のメッシュに追加します。`MergeFillBoundary` が `true` の場合、フィルメッシュの境界頂点を元の穴の境界頂点にマージして、シームレスに結合します。

## 使い方

```csharp
// 穴を埋めたいメッシュ
DMesh3 mesh = ...;
// 穴の境界ループのリスト
List<EdgeLoop> loops = ...;
// 穴が存在する平面
Vector3d planeOrigin = ...;
Vector3d planeNormal = ...;

// PlanarHoleFiller のインスタンスを作成
PlanarHoleFiller filler = new PlanarHoleFiller(mesh);

// 穴のループと平面を設定
filler.AddFillLoops(loops);
filler.SetPlane(planeOrigin, planeNormal);

// (オプション) フィルメッシュの目標エッジ長を設定
filler.FillTargetEdgeLen = 5.0;

// 穴埋め処理を実行
bool success = filler.Fill();

if (success) {
    // 穴埋めが成功しました
    // メッシュは直接更新されています
} else {
    // エラーが発生した場合
    Console.WriteLine($"Failed Insertions: {filler.FailedInsertions}");
    Console.WriteLine($"Failed Merges: {filler.FailedMerges}");
    if (filler.OutputHasCracks) {
        Console.WriteLine("Warning: Output may have cracks at the boundary.");
    }
}
```

## 注意点

-   このクラスは、すべての穴が同一平面上にあることを前提としています。
-   `MeshInsertUVPolyCurve` を内部で使用しており、その処理が失敗した場合（例えば、ポリゴンが自己交差している、または非常に複雑な場合）、`FailedInsertions` がインクリメントされ、穴埋めが不完全になることがあります。
-   フィルメッシュの境界と元のメッシュの境界のマージに失敗した場合、`FailedMerges` がインクリメントされ、`OutputHasCracks` が `true` になることがあります。
-   `MeshPlaneCut` の結果を直接渡して、平面カットによって生じた穴を埋めるための便利なコンストラクタも用意されています。
