# Using WSL

WSL as we mentioned before is a Linux Virtual Machine. This poses some challenges when dealing with your files and using Visual Studio Code.

Luckily VSCode has built in support for VSCode if you install the [WSL Remote Development] extension.

Once you have this installed, VSCode can begin to tightly integrate with your WSL Linux virtual machine.

## Understanding the relationship between Windows and WSL.

One important concept to wrap your mind around is understanding how WSL and Windows work together.  Since WSL is a Virtual Machine, it has it's own networking and it's own filesystem.

### Home Directories

Windows has a home directory on your C: drive in the folder `\Users\<username>`.  Inside of WSL, it's accessible under the path `/mnt/c/Users/<username>`.  

**VERY IMPORTANT** When you are doing development work in WSL, you *should not* use this folder to store your files.

Instead you should store your development files in your Linux home directory, which is located at the path `/home/<username>`

See [this part][wsl-file-storage-guide] of Microsoft's WSL documentation for more info.

## Localhost

Often when developing network applications (like web servers) you will need to have an application listen on an port on `localhost` (127.0.0.1)

The important thing to realize is that Windows has a `localhost`, and Linux ALSO has a `localhost`. These are really separate things. However Windows attempts to notice when you start a server in WSL and it attempts to *forward* requests from the Windows localhost to the Linux localhost.

This is so as you develope your application you can use your Windows web browser to connect to your application running in Linux.

> As of the writing of this version of the guide, there are some bugs with this automatic forwarding system. Also VPN or Anti-virus software can sometimes confuse this auto forwarding system.  As a workaround you can connect to the actual IP address of the WSL virtual machine. To find out the IP address, run this command `ip -br addr show | grep eth0` and then replace the `localhost` in your browser with the ip address it shows.
>
>```shell
> eth0             UP             172.18.250.180/20 > fe80::215:5dff:fe56:7711/64
>```
>
> In this example, the IP address would be `172.18.250.180`

For more information, see the [official docs for WSL Networking][wsl-networking]

## Opening folders from VSCode

You will see a new status bar item in the lower left.

![VSCode Remote Status Bar Item]

You can click on this and it will bring up a menu which lets you open a new VSCode window connected to WSL.

![VSCode WSL Menu]

Once you open a new windows, you will see the status bar item update to reflect you are now connected to WSL.

![VSCode Connected WSL]

Now if you use the open folder button in VSCode it will be browsing the folder contained within your WSL Linux Virtual Machine.

If you open the Integrated Terminal in VSCode, this terminal will contain a shell prompt inside your virtual machine as well.

![VSCode Terminal]

## Opening vscode from the command line

Sometimes it's easier to start from the command line and open your folders containing your projects.

Open Windows Terminal and navigate to the folder you are keeping your projects in using `cd`. See the [Shell Basics] guide if you need help doing this.

Then once you are in the project folder, just run the following command:

```sh
code .
```

The `.` in Unix means *current folder*. So this opens up VSCode already connected to WSL and opens up your current project in it. This is often a faster and easier way to open your projects than navigating the graphical user interface.

## Accessing your files from Windows

Sometimes you may want to copy files in and out of the WSL machine. Since it's a separate computer this can be tricky.  Luckily Microsoft has provided us a way.

There's a special Network Share created in Windows that lets you access WSL's filesystem from Windows Explorer.

To access it, open a Windows Explorer window, and in the location bar type the following and hit enter

```text
\\wsl$
```

You should see a network share called `Ubuntu`. This is the entire contents of your Linux virtual machine's filesystem.

![Windows-Explorer-WSL]

Open it up and then open the folder named 📁`home`

You should see your home folder (named the same as your username) now.  For easy access so you won't have to go through these steps all the time, right click on your home folder and choose "Pin to Quick Access". This puts a link in your Windows Explorer sidebar so you can quickly get to your WSL home folder.

![WSL-Pin-to-Quick-Access]

![WSL-Home-Folder-Contents]

> **IMPORTANT!** Don't blindly drag files around inside the Linux filesystem, stick to only changing things in your home directory, preferably in a folder you've created to hold your projects.

**Note:** If you drag files into your home folder from Windows, they may cause weird files ending in `.Zone.Identifier` to get created. These are extra files created by Windows which keep extra info about the download time of a file and other types of meta data. It is usually safe to just delete them. You can try this procedure to disable the creation of these files: [How to get rid of Zone Identifier Files]

## Opening Windows Explorer from the WSL Shell

You can also open up Windows Explorer to any folder your Linux shell prompt is in.

You can use this command:

```shell
explorer.exe .
```

Just like with VSCode, the `.` here represents the current folder. This should open up Windows Explorer to the exact folder your shell is already in.

If you've used our Ansible playbook to install your machine, it adds a alias to this called `open` so you can be cool like those macOS users and do:

```shell
open .
```

## Rebooting your WSL Linux Virtual Machine

Sometimes you might want to restart your Linux virtual machine for one reason or another.

To do this, close any VSCode or Terminal Windows that are using your WSL and open a Powershell Window.

Then type the following:

```powershell
wsl --shutdown
```

Then open a new Windows Terminal, the WSL machine will automatically start back up.

[Shell basics]: ../common/shell-basics.md
[WSL Remote Development]:vscode:extension/ms-vscode-remote.remote-wsl
[VSCode Remote Status Bar Item]:https://code.visualstudio.com/assets/docs/remote/wsl-tutorial/remote-status-bar.png
[VSCode WSL Menu]:https://code.visualstudio.com/assets/docs/remote/wsl-tutorial/remote-wsl-commands.png
[VSCode Connected WSL]:https://code.visualstudio.com/assets/docs/remote/wsl-tutorial/wsl-status-bar.png
[VSCode Terminal]:https://code.visualstudio.com/assets/docs/remote/wsl-tutorial/new-terminal-in-wsl.png
[Windows-Explorer-WSL]:images/Windows-Explorer-WSL.png
[WSL-Pin-to-Quick-Access]:images/WSL-Pin-to-Quick-Access.png
[WSL-Home-Folder-Contents]:images/WSL-Home-Folder-Contents.png
[How to get rid of Zone Identifier Files]:https://blog.realhe.ro/how-to-get-rid-of-zone-identifier-files/
[wsl-file-storage-guide]:https://docs.microsoft.com/en-us/windows/wsl/setup/environment#file-storage
[wsl-networking]:https://docs.microsoft.com/en-us/windows/wsl/networking