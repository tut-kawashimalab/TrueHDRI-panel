# TrueHDRI-panel
[English docs](./README_en.md)

TrueHDRI-panel は、HDRI を光源環境としてセットアップする Unreal Engine のプラグインです。


## 特徴
- ゲームのランタイム環境、映像コンテンツ制作用途それぞれに対応した適切なライティング環境を構築することが可能です。
  - パストレーシング環境での Look-Dev に最適な、すべての輝度を Emissive とした光源環境の構築
  - Lumen環境や Lightmapping を事前計算するゲームのランタイム環境に合わせた Look-Dev を行うための、輝度の一部を平行光源化した光源環境の構築
- 光源環境アセットの利用時に必要な Sky Light Recapture が自動で行われるようになります。


本プラグインは、[株式会社バンダイナムコスタジオ](https://www.bandainamcostudios.com/)と[東京工科大学](https://www.teu.ac.jp/)との共同研究プロジェクトの一環として制作されました。  
TrueHDRIとは: https://www.bandainamcostudios.com/projects/truehdri


## プラグインの使い方

### セットアップ
1. プラグインを利用したいプロジェクトの .uproject と同じフォルダ内に Plugins という名前のフォルダがない場合は、これを作成してください。
2. Plugins フォルダ内に、プラグインZipファイルの中身をコピーしてください。  
最終的な階層状態は以下の通りです。
[プロジェクトフォルダ]/Plugins/TrueHDRIPanel/TrueHDRIPanel.uplugin
![](./docs_attachments/installation_1.png)

プロジェクトを起動し、Edit > Plugins から TrueHDRIPanel プラグインおよび OpenCV プラグインが有効化されていることを確認してください。  
自動での有効化が行われていない場合は、チェックボックスから有効化を行ってください。  
![](./docs_attachments/installation_2.png)

### HDRI から 光源環境Blueprint を生成する
- テクスチャアセットの用意
EXR形式のテクスチャ画像を Unreal Engine のプロジェクトに読み込みます。  
＊EXR画像がsRGBカラースペースに変換されたものであることを確認してください。  
読み込んだテクスチャアセットの Level Of Detail > Mip Gen Settings を NoMipmaps に設定してください。これにより、テクスチャ画像がフィルタリングの掛からない高いの解像度で利用されるようになります。
![](./docs_attachments/usage_3.png)

- プラグインパネルの呼び出し
トップバーの Windows > TrueHDRIPanel からプラグインのパネルを呼び出してください。
![](./docs_attachments/usage_1.png)
![プラグインパネル](./docs_attachments/usage_2.png)

ここでは二段目の HDRI To Lighting Environments を利用します。

- 光源環境アセットの生成
コンテンツドロワーのテクスチャアセットを HDRI To Lighting Environments > Textures にドラッグ&ドロップして、光源環境化する HDRI を指定してください。
![](./docs_attachments/usage_4.png)

### レベル上で使用する光源環境Blueprintの選択
プラグインパネルの一段目 Current Lighting Environment > Lighting Rig より
事前に生成した光源環境アセットを選択すると、そのアセットが現在のレベル上に反映されます。  
また選択時には Sky Light アクター が自動生成され、新しい光源環境Blueprint を選択する度にSky Light Recapture が行われます。


## 生成用設定値の説明
- Asset Output Path：
  - 生成される光源環境のBlueprint を保存するディレクトリを、Game ディレクトリから指定します
- Allow Overwriting：
  - 同じテクスチャアセットから過去に生成した光源環境がある場合の上書きを許可します
- Main Light Threshold Type：
  - 画像内の輝度の一部を平行光源化するときに、平行光源化を行う輝度の範囲を決める方法について選択します
    - Percentile パーセンタイル値のうち、しきい値を超えるもの
    - Percent 画像内の最低輝度と最大輝度の間のパーセントで、しきい値を超えるもの
    - Percent Ave Max 画像内の平均輝度と最大輝度の間のパーセントで、しきい値を超えるもの
    - Direct Value 指定された値を輝度としてあつかい、それを超える輝度のもの
- Main Light Threshold：
  - 上記設定で指定された種類の輝度値を設定します
- 生成するアセットの種類：
  - With Directional Light しきい値を超える輝度をテクスチャ画像から抜き取り平行光源化します
    - `-Directional` のサフィックスがつきます
  - Emissive Only テクスチャ画像が持つ輝度をそのまま Emissive として利用します
    - `-PathTrace` のサフィックスがつきます
  - Create Both 上記二種類のアセットを両方生成します  
  設定ののちに Create Lighting Rig ボタンを押すことで、以下のアセットが生成されます:
    - 光源環境Blueprint BP_[テクスチャ名] (Asset Output Pathに)
    - 全球画像のマテリアル M_[テクスチャ名] (Asset Output Pathに)
    - (平行光源化した場合) 平行光源化された部分が差し引かれたテクスチャアセット [テクスチャ名]-NoSun (元テクスチャーアセットの隣に)


## 更新履歴
- 2024/06/25
  - プラグイン v1.0.0 公開開始
- 2024/05/28
  - GitHUB readme に利用ドキュメントを追加
