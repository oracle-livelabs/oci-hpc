# Access your GPU Node

## Introduction
In this lab, you will access your GPU Node.

Estimated Lab Time: 5 minutes

## Task: Access your GPU Node
1. Once your job has completed successfully in Resource Manager, you can find the Public IP Addresses for the GPU node and the Private Key on the lower left menu under **Outputs**. 

2. Copy the Private Key onto your local machine, change the permissions of the key and login to the instance:

    ```
    chmod 400 /home/user/key
    ssh -i /home/user/key opc@ipaddress

    ```
3. Once logged into your GPU node, you can run Gromacs from /mnt/block. Refer to the README.md file for specific commands on how to run Gromacs on your GPU instance.

## Acknowledgements
* **Author** - High Performance Compute Team
* **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
* **Last Updated By/Date** - Samrat Khosla, October 2020


