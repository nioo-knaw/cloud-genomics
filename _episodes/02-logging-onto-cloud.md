---
title: "Logging onto Cloud"
teaching: 15
exercises: 5
questions:
- How do I connect to an instance on the SURFsara's cloud?
objectives:
- Log onto to the cloud  user interface
- Start a VM based on a template
- Log off from a running instance
keypoints:
- You can use one set of log-in credentials for many instances
- Logging off an instance is not the same as turning off an instance
---

<script language="javascript" type="text/javascript">
function set_page_view_defaults() {
    document.getElementById('div_aws_win').style.display = 'block';
    document.getElementById('div_aws_unix').style.display = 'none';
};

function change_content_by_platform(form_control){
    if (!form_control || document.getElementById(form_control).value == 'aws_win') {
        set_page_view_defaults();
    } else if (document.getElementById(form_control).value == 'aws_unix') {
        document.getElementById('div_aws_win').style.display = 'none';
        document.getElementById('div_aws_unix').style.display = 'block';
        document.getElementById('div_hpc').style.display = 'none';
        document.getElementById('div_cyverse').style.display = 'none';
    } else {
        alert("Error: Missing platform value for 'change_content_by_platform()' script!");
    }
}

window.onload = set_page_view_defaults;
</script>

## Important Note

This lesson covers how to start a VM based on a already created image and template, and log in and out of a running instance

If you're returning post-workshop and want to launch your own instance, use follow the [SURFsara tutorial](https://doc.hpccloud.surfsara.nl/tutorials/) on how to create a template and instance from scratch.

## Background

To save time, your instructor configured a template for a virtual machine for you prior
to the workshop, and connected it to our lesson data.

## Access the User Interface

The User Interface (UI) is the web site that allows you to manage your _Virtual Machines_ (_VMs_) on the HPC Cloud.

### Log in to the UI

>**Note**
>
>You will receive your access credentials from the workshop facilitators.

* Open the UI page in your browser: [https://ui.hpccloud.surfsara.nl/](https://ui.hpccloud.surfsara.nl/)
* Enter the username & password provided
* Hit the `Login` button

<!---
#### Switch to "user view"

The interface supports several so-called views, which are a way to arrange information on the screen. For this course you should use the _user view_.

* Locate the *buddy* icon <i class="fa fa-user"></i> with your user name at the top-right corner of the screen and click it.
* In the drop-down menu, move to *<i class="fa fa-eye"></i> Views* and in that sub-menu select *user*.
-->

### Add your public SSH key

To complete the setup of your HPC Cloud account, you need to **add a Secure Shell (SSH) public key to your UI account**. This is a one-time task!

>**Note**
>
>If you are not familiar with the SSH authentication method, please read about it on our [documentation page](https://doc.hpccloud.surfsara.nl/SSHkey).


First, you need to own an SSH private/public key pair.

To create a new SSH key pair:

1. Open a terminal on Linux or macOS, or Git Bash / WSL on Windows.
2. Generate a new ED25519 SSH key pair:

```ssh-keygen -t ed25519 -C "email@example.com"```

Or, if you want to use RSA:

```ssh-keygen -o -t rsa -b 4096 -C "email@example.com"```

The -C flag adds a comment in the key in case you have multiple of them
and want to tell which is which. It is optional.


3. Next, you will be prompted to input a file path to save your SSH key pair to.
If you don't already have an SSH key pair, use the suggested path by pressing
Enter. Using the suggested path will normally allow your SSH client
to automatically use the SSH key pair with no additional configuration.

If you already have an SSH key pair with the suggested file path, you will need
to input a new file path and declare what host
this SSH key pair will be used for in your ~/.ssh/config file.


4. Once the path is decided, you will be prompted to input a password to
secure your new SSH key pair. It's a best practice to use a password,
but it's not required and you can skip creating it by pressing
Enter twice.
If, in any case, you want to add or change the password of your SSH key pair,
you can use the -pflag:
```ssh-keygen -p -o -f <keyname>```

Next, you need to copy the public SSH key (`id_rsa.pub`) to the UI. The matching private key (`id_rsa`) remains safe in your laptop.

* Copy the content of your **public** SSH key to the clipboard (e.g. `cat ~/.ssh/id_rsa.pub`, then select and copy all the text)
* Go to the [UI](https://ui.hpccloud.surfsara.nl/) and select *<i class="fa fa-cog"></i> Settings* from the *buddy* icon  <i class="fa fa-user"></i>
* Locate the section _Public SSH Key_ and click on the blue edit icon <i class="fa fa-pencil-square-o" style="color:#0098c3;"></i>
* Paste the content of your public SSH key file into the text box
* There is no _Save_ button. Click outside the text box to complete your action
* Briefly check the contents of the text box against your public key and verify they match: it should start with `ssh-rsa AAAAB`...

## My first VM

Working with the HPC Cloud service mostly revolves around building and destroying Virtual Machines. This section will guide you through the process of building a VM running Linux. Here's an overview of the main steps you will be taking:

* Clone a template and images `image` with a Linux operating system installed
* Reviewing the VM attributes defined in the `template`
* Instantiating the `template` to run your first VM
* Accessing your VM and gracefully shut it down

Let's create your first VM to be run on the HPC Cloud Oort!

### Cloning a template

* Go to the _<i class="fa fa-file-o"></i> VMs_ tab under _Templates_ on the left menu
* Find the `template` with the name "carpentry-genomics-wageningen" and check the tick-box 
* Click on the blue Clone button in the top menu
* Give the new template a unqiue name, for example adding your name in the end.
* Click on `Clone with Images`

### Reviewing the Template  

A `template` consists of a set of attributes that define how a Virtual Machine should look like. For example, how many cores do you want your VM to have? How much RAM memory? What storage drives to attach? Which network connections, etc. ? You will have to adapt the `template` you imported from the _Apps_ list, so that the VM(s) you create out of it meet the requirements you have.

For this part of the course, we would like you to edit the imported `template` following these steps:

* Go to the _<i class="fa fa-file-o"></i> VMs_ tab under _Templates_ on the left menu
* Find the `template` created previously and check the tick-box
* Click on the blue _Update_ button to start editing the template
* Browse through the different tabs there (i.e. _General_, _Storage_, _Network_, ...) to get acquainted with their contents
* Verify that your VM will have internet access:
  * Select the _<i class="fa fa-globe"></i> Network_ tab which shows the network interfaces (`NIC`) for your VM
  * The feedback below tells that the internet interface `NIC 0` on the left pane is mapped to `internet`  

![youselectednetwork](/images/youselectednetwork.png)
* Check the _<i class="fa fa-exchange"></i> Input/Output_ tab:
    * In the _Graphics_ section, the _VNC_ radio button must be selected
    * In the _Inputs_ section, make sure an entry _table USB_ is listed

If this is not the case, then under the _Inputs_ section select _Type_ **Tablet** and _Bus_ **USB** from the drop-down lists, and finally click the _Add_ button next to those drop-down lists.

* If you made any changes to the `template`, click the green button _Update_ at the top, to save your changes.

### Starting the VM

As mentioned earlier, a `template` is just a description of the Virtual Machine that we want to build. Let's create the actual VM from it.

* Go to the _<i class="fa fa-th"></i> VMs_ section below _Instances_ on the left menu

An overview of all existing VMs, that you have the priviledges to see, are displayed.
This list is (probably) empty at the moment, because you have not yet started any VM.

* Click the button _<i class="fa fa-plus" style="background-color:#43AC6A;border-color:#368a55;color:#fff;padding:1px 1ex 1px 1ex;"></i>_ to bring up a "Create Virtual Machine" screen
* Select the *First Template* by clicking once on it
* Inspect the `template` attributes, for the time being do not change them (in particular, leave _Number of instances_ at 1)
* Click on the green _Create_ button at the top of the screen
* Refresh the list of VMs by clicking button <i class="fa fa-refresh"></i> at the top. You will see the _status_ of the VM change


### What happened?

Congratulations! You have just created a fresh, clean Virtual Machine!

> **Note**  
>Your VM will appear in the list of Virtual Machines. At first, it will have the state `PENDING`. This indicates that the HPC Cloud is looking for a place where your VM can actually run. Finding the right place depends on the amount and types of resources (cores, memory, disk...) you requested in the `template`. Keep refreshing the list by clicking the _refresh_ button <i class="fa fa-refresh"></i>. When the required resources become available, your VM will show the status `RUNNING`. Only then you will be able to make use of it.

Let's summarise what you have seen so far. Click on each of the tabs on the left menu and inspect the information provided. The most important ones at this point are:

* _Instances_ &gt; _<i class="fa fa-th"></i> VMs_: here you can manage your VM(s) (e.g.: create, terminate, ...). When you click anywhere on a running VM row (except the tick-box) you can inspect the extended information for that VM in the different tabs. You can even change some VM features from these tabs.
* _Templates_ &gt; _<i class="fa fa-file-o"></i> VMs_: here you can manage your templates. The `template` gives your VM the shape you want. It is just a recipe, and not the machine itself.
* _Storage_ &gt; _<i class="fa fa-download"></i> Images_: here you can manage storage places. You can look at `images` as hard drives.
* _Storage_ &gt; _<i class="fa fa-cloud-download"></i> Apps_: here you can see the list of `appliances` endorsed by SURFsara HPC Cloud team. One `app` of course is a bundle of an `image` and a `template`, which provide a basic working set of installed software and configured properties that allow you to easily create and use a VM.


#### <a name="logging_in_to_the_virtual_machine"></a> Logging in to the Virtual Machine

You can interact with your VM in several ways: command-line (e.g.: SSH), VNC (UI in your browser) or a remote desktop. We will use SSH in a terminal for the time being.

In order to log in to your VM, you will make use of the SSH public key [stored in your profile](#add-your-public-ssh-key) earlier. Proceed as follows:

* **On the UI:** find the VM IP address

The IP address of a VM is shown in the _IPs_ column on the VM list, and in the _Network_ tab of the VM details page.

* **On your laptop:** start a terminal

* **On your laptop:** type the following command on the terminal to establish a connection with your VM

>**Note**
>
>Replace 145.100.5Q.RST with your IP address!

```sh
ssh ubuntu@145.100.5Q.RST
```

<div style="width:90%; margin-left:auto; margin-right:auto; margin-top:1ex; margin-bottom:1ex;" class="alert alert-info" markdown="1">
<i class="fa fa-info-circle fa-2x" aria-hidden="true"></i>

You may receive a message like the following:

```bash
WARNING: Your password has expired.
You must change your password now and login again!
Changing password for ubuntu.
(current) UNIX password:
```

In that case, you will have to "change" the password for user `ubuntu`. Type in the old password (you will not see any characters being typed, but that is expected), which is: `ubuntu` (without the quote marks). Then hit Enter. After that, type in a new password that you will know, followed by the Enter key. And type that new password again. 

You may be logged out after a successful password change. Type the `ssh` command again that you typed before you were prompted to change the password.
</div>


If everything went well, the first time you try to log in your terminal will ask you to add the VM IP to the list of known hosts. Type *Yes*, in that case.

You should now see a similar line in your terminal: `ubuntu@packer-ubuntu-14:~$`.
This is the prompt of your VM and is waiting for your input.
You have logged in successfully!

* Look around a bit, make yourself familiar with the system:

```sh
ubuntu@packer-ubuntu-14:~$ ls /
ubuntu@packer-ubuntu-14:~$ whoami
```

* Create a file:

```sh
ubuntu@packer-ubuntu-14:~$ echo "Hello HPC Cloud!" > myfile
ubuntu@packer-ubuntu-14:~$ cat myfile
```

* Logout by typing `logout` or `ctrl-D` in your terminal (do **not** issue any shutdown command):

```sh
ubuntu@packer-ubuntu-14:~$ logout
```

> <i class="fa fa-question-circle" aria-hidden="true"></i> **Food for brain**
>
> Log in to your VM again. *Is your file still there?*


### <a name="first_shutdown"></a> First shutdown

Let's shutdown your VM. Whenever you do not need your VM running, you should shut it down to stop consuming the resources allocated.

* Go to the list of running VMs in the Cloud UI (_Instances_ &gt; _<i class="fa fa-th"></i> VMs_)
* Tick the box to the left on the row with your VM
* At the upper right corner of the screen, click the red button <i class="fa fa-trash-o" style="background-color:#f04124;border-color:#cf2a0e;color:#fff;padding:1px 1ex 1px 1ex;"></i> and click _Terminate_
* A confirmation action is needed, click OK
* Refresh the list of VMs (_<i class="fa fa-refresh"></i>_) until your VM is gone

You can always boot the "same" VM again whenever you need it, from the corresponding `template`.

> <i class="fa fa-question-circle" aria-hidden="true"></i> **Food for brain**
>
> When the VM has been shut down and disappeared from the list, check and refresh the _Storage_ &gt; _<i class="fa fa-download"></i> Images_ and _Templates_ &gt; _<i class="fa fa-file-o"></i> VMs_ tabs. *Are your `image` and `template` still there?*
>
> The HPC Cloud has hundreds of users. Many of them have common questions. In order to address these we have put together a web site with some documentation, we call it the HPC Cloud Documentation. Do you know the URL of this web site? Make sure you find out!
