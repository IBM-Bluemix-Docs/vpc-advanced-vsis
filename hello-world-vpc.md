---

copyright:
  years: 2018
lastupdated: "2018-12-11"

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:new_window: target="_blank"}
{:pre: .pre}
{:tip: .tip}
{:table: .aria-labeledby="caption"}
{:download: .download}

# Creating a VPC using the CLI

This guide shows how you can use the {{site.data.keyword.cloud}} CLI to create a VPC, subnet, and virtual server instance, and then associate a floating IP address to enable your instance to communicate with the internet. 

## Before you begin
{: #before}
Make sure you [set up your environment](set-up-environment.html).


## Step 1: Log in to IBM Cloud

```
ibmcloud login --sso -a <iam_endpoint>
 ```
{: codeblock}

Replace `iam_endpoint` with the one of the following URLs:
* Staging: https://api.stage1.ng.bluemix.net
* Production: https://api.ng.bluemix.net

This command returns a URL and prompts for a passcode. Go to that URL in your browser and log in. If successful, you will get a one-time passcode. Copy this passcode and paste it as a response on the prompt. After the authentication steps, you'll be prompted to choose an account. Choose the account that was granted access to participate in the beta. Respond to any remaining prompts to finish logging in.

## Step 2: Create a VPC and save the VPC ID

Use the following command to create a VPC named _my-vpc_.

```
ibmcloud is-ng vpc-create my-vpc
```
{: codeblock}

You should see output like this:

```
Creating VPC my-vpc in resource group Default under account <Account Name> as user <User>...

ID        ba9e785a-3e10-418a-811c-56cfe5669676   
Name      my-vpc   
Default   no   
Created   1 second ago   
Status    available   
Tags      -   
```
{:screen}

Save the ID in a variable so you can use it later, for example:

```
vpc="ba9e785a-3e10-418a-811c-56cfe5669676"
```
{: codeblock}

## Step 3: Create a subnet and save the subnet ID

Let's pick the `us-south-3` zone for the subnet's location and call the subnet _my-subnet_.

```
ibmcloud is-ng subnet-create my-subnet $vpc us-south-3 --ipv4_cidr_block "10.0.1.0/24"
```
{: codeblock}

You should see output like this:

```
Creating Subnet my-subnet in resource group Default under account <Account Name> as user <User>...

ID               50ba0da9-279a-4791-b7cb-cd3d7b2bc14d   
Name             my-subnet   
IPv*             ipv4   
IPv4 CIDR        10.0.1.0/24   
IPv6 CIDR        -   
Addr available   0   
Addr Total       0   
Gen              -   
ACL              allow-all-network-acl-ba9e785a-3e10-418a-811c-56cfe5669676(e9c2790b-cee2-465a-8539-d8cd90d33621)   
Gateway          -   
Created          now   
Status           pending   
Zone             us-south-3   
VPC              my-vpc(ba9e785a-3e10-418a-811c-56cfe5669676)   
Resource Group   -   
Tags             -   
```
{:screen}

Save the ID in a variable so you can use it later, for example:

```
subnet="50ba0da9-279a-4791-b7cb-cd3d7b2bc14"
```
{: codeblock}

The status of the subnet is `pending` when it's first created. Before you can proceed, the subnet needs to move to the `available` status, which takes a few seconds. To check the status of the subnet, run this command:

```
ibmcloud is-ng subnet $subnet
```
{: codeblock}

## Step 4: Create an SSH key in IBM Public Cloud.

You'll use the key to provision an instance. You can use one key to provision multiple instances.

To see the available keys in your IBM Cloud account, run this command:

```
ibmcloud is-ng keys
```
{: codeblock}

To create a key, run the following command. Substitute the path to your `id_rsa.pub` file.

```
ibmcloud is-ng key-create my-key @$HOME/.ssh/id_rsa.pub
```
{: codeblock}

You should see output like this:

```
Creating key my-key in resource group Default under account <Account Name> as user <User>...

ID               859b4e97-7540-4337-9c64-384792b85653   
Name             my-key   
Type             rsa   
Length           2048   
FingerPrint      SHA256:hkcAOGB5z7QXqZLHd0kGqhj735qrfMjZLH9PxS/42vA   
Key              ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC9i2joQ8eiFVdJ7pOlC6h5+IoBL6wFFygkk9Na3gV8Bi52dv44YOAjSJ2oduguHEtLp5r4eh4+5jiEBkFYkHNUhE0MxlcVZABTYWePXx4QnlmGr99xyOfi6DAHhSRQiSBhmhjcGjbADavDuIgoyKpVXbU9O1If3P0miNpaouaZmr+d68OHt4yPvNnztlluV3JBISJgqJ7pzg6wFF0SrjqtEYKBd8oxwogHu+rmRgT7IF09oSiKpKZRF0VfeLFz+REh9RuKa4Jh63aa2PRIgDKq6HO7MEdfOtGzCzoqqlFKgpl+VgGyT0b5BjQEnWv13cwx02bv5QCwma/GeAOpW0CD user@email.com   

Created          now   
Resource Group   -   
Tags             -   
```
{:screen}

Save the ID in a variable so you can use it later, for example:

```
key="859b4e97-7540-4337-9c64-384792b85653"
```

## Step 5: Select a profile and image for the instance

Let's pick instance profile `b-4x8` and image `ubuntu-16.04-amd64`. To get the image ID of `ubuntu-16.04-amd64`, run the following command:

```
image=$(ibmcloud is-ng images | grep "ubuntu-16.04-amd64" | cut -d" " -f1)
```
{: codeblock}

## Step 6: Provision an instance

```
ibmcloud is-ng instance-create my-instance $vpc us-south-3 b-4x8 $subnet 1000 --image $image --keys $key --bandwidth 25
```
{: codeblock}

You should see output like this:

```
Creating instance my-instance in resource group Default under account <Account Name> as user <User>...

ID                4562c5c0-9cf7-4406-bc90-ab4baea91057   
Name              my-instance   
Gen                  
Profile           b-4x8   
CPU Arch          amd64   
CPU Cores         4   
CPU Frequency     2000   
Memory            8   
Primary Intf      primary(2e850924-b5d7-4386-a778-03556d5850c1)   
Primary Address   10.0.1.5   
Image             ubuntu-16.04-amd64(7eb4e35b-4257-56f8-d7da-326d85452591)   
Status            pending   
Created           5 seconds ago   
VPC               my-vpc(ba9e785a-3e10-418a-811c-56cfe5669676)   
Zone              us-south-3   
Resource Group    -   
Tags              -   
```
{:screen}

Save the ID of the primary network interface `primary intf` in a variable so you can use it later, for example:

```
nic="2e850924-b5d7-4386-a778-03556d5850c1"
```
{:codeblock}

The status of the instance is `pending` when it's first created. Before you can proceed, the instance needs to move to the `running` status, which takes a few minutes. To check the status of all instances, run this command:

```
ibmcloud is-ng instances
```
{: codeblock}


## Step 7: Create a floating IP address

You need the floating IP address so you can log in to the instance from the internet.

```
ibmcloud is-ng floating-ip-reserve my-fip --nic $nic
```
{: codeblock}

You should see output like this:

```
Creating floating ip my-fip in resource group Default under account <Account Name> as user <User>...

ID               b9d1cc1f-67db-40e3-81de-9228465170a5   
Address          169.61.181.53   
Name             my-fip   
Target           primary(2e850924-.)   
Target Type      intf   
Target IP        10.0.1.5   
Created          now   
Status           available   
Zone             us-south-3   
Resource Group   -   
Tags             -   
```
{:screen}

Save the `Address` in a variable so you can use it later:

```
address=169.61.181.53
```
{: codeblock}


## Step 8: Log in to your instance using your private SSH key

For example, you can use a command of this form:

```
$ ssh -i $HOME/.ssh/id_rsa root@$address
```
{:codeblock}

 You'll receive a response similar to the example that follows. When you're prompted to continue connecting, type `yes`.

```
The authenticity of host '169.61.181.53 (169.61.181.53)' can't be established.
ECDSA key fingerprint is SHA256:9MczXIwJq892DYwu0sZpQZOXORdvNXeP1aFioZpQDsM.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '169.61.181.53' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.4.0-133-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

root@my-instance:~#
```
{:screen}

## Step 9: Hello, World!

Run the following command in the terminal window:

```
echo `hostname` says "Hello, World!"
```
{:codeblock}

You should see the following output:

```
my-instance says Hello, World!
```
{:screen}