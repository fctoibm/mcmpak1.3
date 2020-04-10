Installing MCM Pak  1.3 
===============================


The guide will get you up and running with MCM Pak 4.x. on OCP 4.3. The setup assumes OCP 4.3 is already installed and administration rights access available to deploy the MCM Pak.
The guide assumes OCP 4.3 installed using the same GitHub repository if this is not the case, please make sure the OCP worker node capacity is matched following table:  

## Hardware requirements

| Software      | vCPU   | Mem  | HDD | Node
| ------          | ------ |----  | --- | ------ |
| Worker Node  | 12  | 24 | 150 | 1|
| Worker Node | 12  | 24 | 150 | 1 |
| Worker Node |  12 | 24 | 150 | 1 |
| Total |  36 | 72 | 450 | 3 |
> **NOTE:** You can add more worker node to meet the mentioned capacity or update the original VM's CPU/MEM  using the virtual manager console.  

Deploying MCM Pak
------------------------------------

The installation support two options VMWare or KVM install, and both will deploy VM guest if required. The VM guest called PakHelper node act as a client to install MCM Pak. There is no reason to deploy a VM guest client if you already have VM guest centos 7 OS available in the same network. If this is already in place, please skip the option one and two, but in case you don't have VM guest available, please execute both option 1 or 2 and 3. Please follow the Flowchart to figure out which steps need to be performed on your environment.

![Flow Chart](images/flow_chart.png)
 
Option One (KVM Only)
--------------------
--------------------

### Prepare the Host KVM ####

Login to the Host KVM as root and execute the following commands.
```
cd /opt
git clone https://github.com/fctoibm/mcm_1.3.git
cd /opt/mcm_1.3
```

Edit the [kvmvars.yaml](./kvmvars.yaml) file with the IP addresses that will be assigned to the pakhelper node. The IP addresses need to be right since they will need to access the OpenShift servers.
Edit the [hosts](./hosts) file vmguest section to match the pakhelper node information. This should be similar to kvmvars.yaml file


> **NOTE:** If the setup is performed on KVM host infrastructure, then delete the iptables forward rules by issuing following commands on KVM host server

```
iptables-save > /root/savedrules_pak.txt
iptables-restore < /root/savedrules.txt
```

### Execute the Playbook ###
 
This play book will create MCM Pak Helper VM guest on KVM Host. The new VM will access the OCP servers and  public network access to download yum packages.

> **NOTE:** If the client is already available, please proceed to option 3.

```
Run the playbook to setup the kvmhost
ansible-playbook -v -e @kvmvars.yaml play.yaml -t kvm --limit "kvmhost"
```



Option Two (VMWare Only)
-----------------------
-----------------------
Login to the client VM as root and execute the following commands.
```
cd /opt
git clone https://github.com/fctoibm/mcm_1.3.git
cd /opt/mcm_1.3
```

Edit the [vmwarevars.yaml](./vmwarevars.yaml) file with the IP addresses that will be assigned to the pakhelper node. The IP addresses need to be right since they will need to access the OpenShift servers.
Edit the [hosts](./hosts) file vmguest section to match the pakhelper node information. This should be similar to vmwarevars.yaml file


### Execute the Playbook ###
 
This play book will create MCM Pak Helper VM guest on VMware Host. The new VM will access the OCP servers and  public network access to download yum packages.


> **NOTE:** If the client is already available, please proceed to option 3.

```
Run the playbook to setup the vmwarehost
ansible-playbook -v -e @vmwarevars.yaml play.yaml -t vmware --limit "vmwarehost"
```


Option Three (Client Only)
--------------------------
--------------------------
Depending on the above flow the -e @<input variable YOUR_VARS.YAML> could be vmwarevars.yaml or kvmvars.yaml

```
ansible-playbook -vv -e @YOUR_VARS.YML play.yaml -t setupmcmpak --limit "vmguest"

```
> **NOTE:**: If the install fails uninstall the MCM Pak and then run the resinstall commmand


```
ansible-playbook -v -e @YOUR_VARS.YML  play.yaml -t uninstall --limit "vmguest"
ansible-playbook -v -e @YOUR_VARS.YML  play.yaml -t reinstall --limit "vmguest"


```


##### Update IP tables on KVM Host to access OpenShift URL #####


On KVM Host run the following commands:
```
iptables-restore < /root/savedrules_pak.txt
```


After MCM Pak Installation 
------------------------------------
Follow the [After installation](https://www.ibm.com/support/knowledgecenter/SSFC4F_1.3.0/kc_welcome_cloud_pak.html/ "After installation link")  section to verify the install 


