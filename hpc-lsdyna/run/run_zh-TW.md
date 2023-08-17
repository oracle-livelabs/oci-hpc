# 執行 LS-DYNA

## 簡介

在此實驗室中，您將執行 LS-DYNA。

預估實驗室時間：10 分鐘

## 執行 LS-DYNA

執行 LS-DYNA 很簡單。若要指定需要執行的主機，您需要建立一個機器檔案。您可以依下列方式產生，或手動產生。Platform 和 IntelMPI 的格式為 hostname:corenumber。

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

某些執行參數可由參數檔指定：pfile

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

此特定 pfile 會告知 LSDyna，不要傾印至磁碟上許多資訊。使用記憶體來儲存本機檔案，並將全域檔案儲存至 /mnt/nfs-share/benchmark/one\_global\_dir。

如果本機檔案不適於記憶體，ANother 將其儲存為 /mnt/localdisk/tmp，可使用電腦上的本機 NVMe 來儲存這些檔案。

若要在多個節點上執行，請將模型置於共用磁碟機 (Ex:/mnt/nfs-share/work/)。這裡提供的範例是執行 3 輛車型。您可以將它新增至安裝程式之類的物件儲存，或將它下載至機器。

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

確定您已設定 mpi 的所有正確變數以正確執行。使用下列 Intel MPI 指令來執行此指令 (變更模型名稱與核心號碼)：

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

平台 MPI：

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## 特殊案例

對於某些模型而言，Douple Precision 執行檔將不足以分解模型。您可以使用執行檔的雙倍精確度版本，或者可以雙倍精確度執行分解，但還是以單一精確度執行以提高速度。

在分解中，將 ncycles=2 加到指令行，並將此部分加入至 pfile：

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

模型將分解為名為分解的檔案，並儲存在模型的目錄中。在第二次執行期間，LS-Dyna 會看到此檔案，且不會重做分解。

對於「單一」精確度的執行，您可以在 pfile 中使用相同的指令。

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月