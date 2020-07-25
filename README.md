# PIMD-on-singularity

* [@cometscome_phys](https://twitter.com/cometscome_phys)さんの[PIMDとAENETを使って機械学習分子シミュレーションをやってみよう](https://qiita.com/cometscome_phys/items/660a84119b5d4acce903)にある環境構築をSingularityコンテナ定義ファイルに焼き直したものです．
* PIMD, Quantum Espresso, aenetが使用可能です．
* Singularityコンテナにすることで，マシン環境に依存しない計算環境が構築できます．

# Preparation

1. コンテナイメージのビルド用として，root権限で実行可能なマシン環境を準備する
    * (Winの場合)vagrantからBoxをインストールして利用する, WSLを利用するetc.
    * (Mac, Linuxの場合)自分が管理者のマシンを準備．
1. (必要な場合)Singularityをインストールする
1. このリポジトリをダウンロード: `git clone https://github.com/k0syam/PIMD-on-singularity.git`
1. イメージファイルをビルド:
     ```
        cd PIMD-on-singularity
        sudo singularity build PIMD-suite-debian.sif PIMD-suite-debian.def
     ```
1. ビルド済みイメージファイル(.sif)を使用して計算を実行:
     ```
        singularity exec PIMD-suite-debian.sif mpirun -np 4 /opt/PIMD/compile/...
     ```

# Examples

* qe_md
    ```
    singularity exec PIMD-suite-debian.sif cp -r /opt/PIMD/examples/SiO2/qe_md ./
    cd qe_md
    sed -i -e 's/..\/..\/..\/lib\/qe\/pseudo\//\/opt\/PIMD\/lib\/qe\/pseudo\//g' ./qe.dat
    sed -i -e 's/Si.pz-vbc.UPF/Si.pbe-n-rrkjus_psl.1.0.0.UPF/g' ./qe.dat
    sed -i -e 's/O.pz-kjpaw.UPF/O.pbe-n-kjpaw_psl.0.1.UPF/g' ./qe.dat
    singularity exec ../PIMD-suite-debian.sif mpirun -np 4 /opt/PIMD/compile/pimd.mpi.x
    ```

* aenet_pimd_nvt
    ```
    singularity exec PIMD-suite-debian.sif cp -r /opt/PIMD/examples/SiO2/aenet_pimd_nvt ./
    cd aenet_pimd_nvt
    singularity exec ../PIMD-suite-debian.sif mpirun -np 4 /opt/PIMD/compile/pimd.mpi.x
    ```

# Remarks

* コンテナ内のパス
    * PIMDインストール先: `/opt/PIMD`
    * QE擬ポテンシャル保存先: `/opt/PIMD/lib/qe/pseudo`
* 実行にroot権限は必要ない
* 同じバージョンのsingularityが入ったマシンであれば，.sifファイルを持ち込むことで計算実行できる
* Quantum Espresso向け擬ポテンシャルは，SSSPからダウンロードしている
* 依存ライブラリのライセンスはそれぞれに準拠する

# References

* [PIMD](https://ccse.jaea.go.jp/software/PIMD/index.jp.html)
* [Quantum Espresso](https://www.quantum-espresso.org/)
* [Standard solid-state pseudopotentials (SSSP) in Materials Cloud](https://www.materialscloud.org/discover/sssp/table/efficiency)
* [aenet](http://ann.atomistic.net/)
* [Singularity](https://sylabs.io/singularity/)
