---

copyright:
  years: 2018
lastupdated: "2018-12-12"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:important: .important}
{:download: .download}

# Creating a VPC using {{site.data.keyword.cloud_notm}} console
This guide shows how you can use {{site.data.keyword.cloud}} console to create a VPC, subnet, and virtual server instance, and then associate a floating IP address to enable your instance to communicate with the internet. 

Some options in the console are not yet supported. This tutorial covers all features that are supported in beta 1.
{: important} 

## Before you begin
{: #before}

Set up your account to access the beta. Make sure your account is [upgraded to a paid account](../../account/account_faq.html#changeacct){: new_window}. 

Make sure you have an SSH key. The key will be used to connect to the virtual server instance. For example, generate an SSH key on your Linux server by running the following command:
    ```
    ssh-keygen -t rsa -C "user_ID"
    ``` 

   This command generates two files. The generated public key is in the `id_rsa.pub` file under an ``.ssh`` directory in your home directory, for example, ``/Users/<USERNAME>/.ssh/id_rsa.pub``.

## Creating a VPC and subnet

To create a VPC and subnet:

1. Open [{{site.data.keyword.cloud_notm}} console ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://ondeck.console.cloud.ibm.com/vpc-ext){: new_window}
1. Click **Create a VPC**.
1. Enter a name for the VPC, such as `my-vpc`.
1. Enter a name for the new subnet in your VPC, such as `my-subnet`. 
1. Select a location for the subnet. The location consists of a region and a zone. In this beta release, the  available zone is Dallas 3.

    The region you select is used as the region of the VPC. All additional resources you create in this VPC will be created in the selected region.
    {: tip}
1. Enter an IP range for the subnet in CIDR notation, for example: `10.240.0.0/22`. In most cases, you can use the default IP range. If you want to specify a custom IP range, you can use the IP range calculator to select a different address prefix or change the number of addresses.
1. Click **Create virtual private cloud**.
1. To create another subnet in this VPC, click the **Subnets** tab and click **New subnet**. When you define the subnet, make sure to select `my_vpc` in the **Virtual private cloud** field.

## Creating a virtual server instance

To create a virtual server instance in the newly created subnet:

1. Click **Virtual server instance** in the navigation pane and click **New instance**.
1. Enter a name for the instance, such as `my-instance`.
1. Select the VPC that you created.
1. In the **Location** field, select the zone in which to create the instance. 
1. Select an image (that is, operating system and version) such as Debian GNU/Linux 9.x Stretch/Stable.
1. To set the instance size, select one of the popular profiles or click **All profiles** to choose a different core and RAM combination that's most appropriate for your workload.
1. Select an existing SSH key or add a new SSH key that will be used to access the virtual server instance. To add an SSH key, click **New key** and name the key. After you enter your previously generated public key value, click **Add SSH key**.
1. _Optional:_ Enter user data to run common configuration tasks when your instance starts. For example, you can specify cloud-init directives or shell scripts for Linux images. For more information, see [User Data](../../vsi-is/vsi_is_provisioning_scripts.html).
1. Note the boot volume. In the current release, 100 GB is allotted for the boot volume. *Auto Delete* is enabled for the volume; it will be deleted automatically if the instance is deleted.
1. In the **Network interfaces** area, you can edit the network interface and change its name and port speed. If you have more than one subnet in the selected zone and VPC, you can attach a different subnet to the interface. If you want the instance to exist in multiple subnets, you can create more interfaces.
1. Click **Create virtual server instance**. The status of the instance starts as *Pending*, changes to *Stopped*, and then *Powered On*. You might need to refresh the page to see the change in status.

## Reserving a floating IP address

Reserve and associate a floating IP address to enable your instance to be reachable from the internet.  

Your instance must be running before you can associate a floating IP address. It can take a few minutes for the instance to be up and running.
{: tip}

To reserve and associate a floating IP address:

1. On the Virtual server instances page, click your instance to view its details.
1. In the **Network interfaces** section, click **Reserve** for the interface that you want to associate with a floating IP address.

If you later want to reassign this floating IP address to another instance in the same zone, find the floating IP address on the **Floating IPs** page, click its overflow menu (**...**), and click **Unassociate**. Then click  **Associate** to select the instance and network interface that you want to associate with the floating IP address.
{: tip}

## Connecting to your instance
Using the floating IP address that you created, ping your instance to make sure it's up and running:

`ping <public-ip-address>`

### Connecting to Linux images

Since you created your instance with a public SSH key, you can now connect to it directly by using your private key:

`ssh -i <path-to-private-key-file> root@<public-ip-address>`

## Congratulations!

You've successfully created and configured a VPC, subnet, and a virtual server instance. You can continue to develop your VPC by adding more instances and subnets.
