# Access your Cluster

## Introduction
In this lab, you will access your cluster.

Estimated Lab Time: 10 minutes


## Task: Access your Cluster

1. The public IP address of the bastion can be found on the lower left menu under Outputs. If you navigate to your instances in the main menu, you will also find your bastion instance as well as the public IP.

2. The Private Key to access the machines can also be found there. Copy the text in a file on your machine, let's say/home/user/key:
    
    ```
    chmod 600 /home/user/key 
    ssh -i /home/user/key opc@ipaddress 
    ```

## Acknowledgements
* **Author** - High Performance Compute Team
* **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
* **Last Updated By/Date** - Samrat Khosla, October 2020

