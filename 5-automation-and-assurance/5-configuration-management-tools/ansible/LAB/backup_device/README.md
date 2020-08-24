# backup device

Directory structure

```
root@lab-ansible:~/LAB/backup_device# tree
.
|-- ansible.cfg
|-- group_vars
|   |-- ios_routers.yml
|   |-- routers.yml
|   `-- switches.yml
|-- inv.yml
|-- outputs
|   |-- 2020-08-24-csr1.txt
|   |-- 2020-08-24-csr10.txt
|   |-- 2020-08-24-csr2.txt
|   |-- 2020-08-24-csr3.txt
|   |-- 2020-08-24-csr4.txt
|   |-- 2020-08-24-csr5.txt
|   |-- 2020-08-24-csr6.txt
|   |-- 2020-08-24-csr7.txt
|   |-- 2020-08-24-csr8.txt
|   |-- 2020-08-24-custx-cpe1.txt
|   `-- 2020-08-24-custx-cpe2.txt
|-- pb.yml
`-- tasks
    |-- dellos9.yml
    |-- ios.yml
    `-- nxos.yml

```


Use ansible to backup router configs in LAB 


Note: ```csr9``` hasn't been setup yet.  ```csr1``` and ```csr2``` worked before - need to investigate why they failed.


```
root@lab-ansible:~/LAB/backup_device# ansible-playbook pb.yml 

PLAY [PLAY 1: BACKUP device] *******************************************************************************************

TASK [TASK 1: Include tasks for different devices] *********************************************************************
included: /root/LAB/backup_device/tasks/ios.yml for csr1, csr2, csr3, csr4, csr5, csr6, csr7, csr8, csr9, custx-cpe1, custx-cpe2, csr10

TASK [IOS >> Gather Cisco IOS information] *****************************************************************************
ok: [csr1]
ok: [csr2]
ok: [csr3]
ok: [csr4]
ok: [csr5]
ok: [csr6]
ok: [csr7]
ok: [csr8]
fatal: [csr9]: FAILED! => {"msg": "[Errno None] Unable to connect to port 22 on 192.168.250.39"}
ok: [custx-cpe1]
ok: [custx-cpe2]
ok: [csr10]

TASK [TASK 3: Create outputs/folder] ***********************************************************************************
ok: [csr1]

TASK [TASK: Get facts] *************************************************************************************************
fatal: [csr1]: FAILED! => {"msg": ""}
fatal: [csr2]: FAILED! => {"msg": ""}
ok: [csr4]
ok: [csr5]
ok: [csr3]
ok: [csr6]
ok: [csr7]
ok: [csr8]
ok: [custx-cpe1]
ok: [custx-cpe2]
ok: [csr10]

TASK [TASK: Get date] **************************************************************************************************
ok: [csr3]
ok: [csr4]
ok: [csr5]
ok: [csr6]
ok: [csr7]
ok: [csr8]
ok: [custx-cpe1]
ok: [custx-cpe2]
ok: [csr10]

TASK [TASK 4: Write output to file] ************************************************************************************
ok: [csr7]
ok: [csr6]
ok: [csr3]
ok: [csr4]
ok: [csr5]
ok: [csr8]
ok: [custx-cpe1]
ok: [custx-cpe2]
ok: [csr10]

PLAY RECAP *************************************************************************************************************
csr1                       : ok=3    changed=0    unreachable=0    failed=1   
csr10                      : ok=5    changed=0    unreachable=0    failed=0   
csr2                       : ok=2    changed=0    unreachable=0    failed=1   
csr3                       : ok=5    changed=0    unreachable=0    failed=0   
csr4                       : ok=5    changed=0    unreachable=0    failed=0   
csr5                       : ok=5    changed=0    unreachable=0    failed=0   
csr6                       : ok=5    changed=0    unreachable=0    failed=0   
csr7                       : ok=5    changed=0    unreachable=0    failed=0   
csr8                       : ok=5    changed=0    unreachable=0    failed=0   
csr9                       : ok=1    changed=0    unreachable=0    failed=1   
custx-cpe1                 : ok=5    changed=0    unreachable=0    failed=0   
custx-cpe2  

```
