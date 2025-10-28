# memo

- gazebo harmonicではtransform controlというプラグインで移動や回転などをするためのツールを画面上に出現させる
  - ironicとかだとデフォルトは左上に普通に出てきてたはず？sdfにかかれてたのかな
- resource spawnerを使えばfuelやローカルからモデルを持ってこれる
  - もしかしたらあらかじめSpawnプラグインを入れておかないと入れられないかも？
  - ローカルから持ってきたい時は`GZ_SIM_RESOURCE_PATH`にパスを通してあげる
    - `~/.bashrc`にね
- 
