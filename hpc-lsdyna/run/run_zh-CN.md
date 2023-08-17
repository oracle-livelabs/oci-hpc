# 运行 LS-DYNA

## 简介

在本练习中，您将运行 LS-DYNA。

估计实验室时间：10 分钟

## 运行 LS-DYNA

运行 LS-DYNA 非常简单。要指定需要运行的主机，需要创建计算机文件。可以按以下方式生成，也可以手动生成。格式为 Platform 和 IntelMPI 的 hostname:corenumber。

    <copy>
    sed 's/$/:36/' /etc/opt/oci-hpc/hostfile > machinefile
    </copy>
    

某些运行参数可由参数文件指定：pfile

    <copy>
    gen { nodump nobeamout dboutonly }
    dir { global /mnt/nfs-share/benchmark/one_global_dir local /dev/shm }
    
    </copy>
    

此特定 pfile 指示 LSDyna 不向磁盘转储大量信息。使用内存存储本地文件并将全局文件存储到 /mnt/nfs-share/benchmark/one\_global\_dir 中。

如果在内存中不适合存储本地文件的 ANother 位置是使用计算机上的本地 NVMe 来存储这些文件。/mnt/localdisk/tmp

要在多个节点上运行，请将模型放在共享驱动器 (Ex:/mnt/nfs-share/work/) 上。此处提供的示例是运行 3 辆汽车模型。您可以将其添加到对象存储（如安装程序），然后将其下载或 scp 到计算机。

    <copy>
    wget https://objectstorage.us-phoenix-1.oraclecloud.com/p/qwbdhqwdhqh/n/tenancy/b/bucket/o/3cars_shell2_150ms.k
    
    </copy>
    

确保已将 mpi 的所有正确变量设置为正确运行。对于 Intel MPI，使用以下命令运行它（更改型号和核心编号）：

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank \
    -ppn 32 -iface enp94s0f0 -genv I_MPI_FABRICS=shm:dapl -genv DAT_OVERRIDE=/etc/dat.conf -genv I_MPI_DAT_LIBRARY=/usr/lib64/libdat2.so \
    -genv I_MPI_DAPL_PROVIDER=ofa-v2-cma-roe-enp94s0f0 -genv I_MPI_FALLBACK=0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_ADJUST_ALLREDUCE 5 -genv I_MPI_ADJUST_BCAST 1 -genv I_MPI_DEBUG=4 \
    -genv I_MPI_PIN_PROCESSOR_LIST=0-35 -genv I_MPI_PROCESSOR_EXCLUDE_LIST=36-71 \
    /mnt/nfs-share/LSDYNA/ ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_intelmpi-413 
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

对于平台 MPI：

    <copy>
    mpirun -np 256 -hostfile ./hostfile_rank -ppn 32 \ 
    -d -v -prot -intra=shm -e MPI_FLAGS=y -e MPI_HASIC_UDAPL=ofa-v2-cma-roe-enp94s0f0 -UDAPL \
    /mnt/nfs-share/LSDYNA/ls-dyna_mpp_s_r9_2_119543_x64_redhat54_ifort131_sse2_platformmpi \
    i=3cars_shell2_150ms.k \
    memory=1000m memory2=160m p=pfile
    
    </copy>
    

## 特殊情况

对于某些模型，双精度可执行文件不足以分解模型。可以使用可执行文件的双精度版本，也可以执行双精度分解，并且仍以单精度运行以提高速度。

对于分解，将 ncycles=2 添加到命令行，并将此部分添加到 pfile：

    decomposition {								
     file decomposition								
     rcblog rcblog								
    }
    

将在名为 decomposition 的文件中分解模型，并将其存储在模型的目录中。在第二次运行期间，LS-Dyna 将看到此文件，而不是重做分解。

要以单精度运行，可以在 pfile 中使用相同的命令。

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月