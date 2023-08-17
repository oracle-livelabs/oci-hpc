# LS-DYNAの実行

## 概要

この演習では、LS-DYNAを実行します。

予想ラボ時間: 10分

## LS-DYNAの実行

LS-DYNAの実行はかなり簡単です。実行する必要があるホストを指定するには、マシンファイルを作成する必要があります。次のように生成することも、手動で生成することもできます。形式は、プラットフォームと IntelMPIの両方のhostname:corenumberです。

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

一部の実行パラメータは、パラメータ・ファイル(pfile)で指定できます。

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

この特定のpfileは、ディスクに多くの情報をダンプしないように LSDynaに指示します。メモリーを使用してローカル・ファイルを格納し、グローバル・ファイルを/mnt/nfs-share/benchmark/one\_global\_dirに格納します。

ローカル・ファイルがメモリーに収まらない場合は、ローカル・ファイルを格納するANotherの場所が/mnt/localdisk/tmpで、マシン上のローカルNVMeを使用してこれらのファイルを格納します。

複数のノードで実行するには、モデルを共有ドライブに配置します(Ex:/mnt/nfs-share/work/)。ここに示す例は、3台の自動車モデルを実行することです。インストーラなどのオブジェクト・ストレージに追加して、それをダウンロードするか、マシンにscpできます。

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

MPIの正しい変数をすべて正しく実行するように設定していることを確認してください。Intel MPIの次のコマンドを使用して実行します(モデル名とコア番号を変更します)。

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

プラットフォームMPIの場合:

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## 特別ケース

一部のモデルでは、Douple Precision実行可能ファイルはモデルの分解に十分ではありません。実行可能ファイルの倍精度バージョンを使用するか、倍精度で分解を実行し、単精度で実行して速度を上げることができます。

分解については、コマンド行にncycles=2を追加し、この部分をpfileに追加します。

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

モデルは分解というファイルに分解され、モデルのディレクトリに格納されます。2回目の実行中、LS-Dynaはこのファイルを表示し、分解を再実行しません。

単一精度で実行する場合は、pfileで同じコマンドを使用できます。

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月