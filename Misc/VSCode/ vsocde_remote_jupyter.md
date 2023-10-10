# Setting up a VSCode Remote Connection and Running Jupyter Notebooks

## Introduction

This tutorial aims to provide comprehensive guidance for configuring a remote development environment using Visual Studio Code (VSCode). The tutorial assumes you need to connect to a remote server called sleepy or sneezy (UC physics Servers), which are only accessible via an intermediary server named earth. Furthermore, it discusses how to run Jupyter notebooks within the VSCode environment on a remote server.

## Setting Up VSCode Remote Connection

## Prerequisites

- Install Visual Studio Code: Download and install it from [here](https://code.visualstudio.com/).
- Install the Remote - SSH extension in VSCode.
Open VSCode and then go to extensions tab on the left side of editor and search for `Remote - SSH` and install the extension (It is official Microsoft extension so check that)
- Accounts on Earth, Sleepy (or sneezy)

### SSH Connection

You can manually connect to `earth` and then hop from there to either `sleepy` or `sneezy`

- Open VSCode and navigate to the Remote Explorer tab
You will find two options `tunnles` and `SSH` .
- Go to SSH and click on `+`  to add new ssh connection
- In the input field that appears, type `ssh` [`username@`](username@earth.server.address)``[`earth.phy.uc.edu`](earth.phy.uc.edu)``.
- You will be connected to `Earth` Server

Since `sleepy`  and `sneezy`  are not publicly accessible on the internet (You will need to be connected to UC network either on campus or UC VPN) , you'll have to set up an SSH tunnel through earth.
- Open the local terminal.
   - Write the following command  `ssh -L localhost:2222:` [`sleepy.geop.uc.edu`](sleepy.geop.uc.edu)`:22` [`username@`](username@earth.server.address)``[`earth.phy.uc.edu`](earth.phy.uc.edu)``  You should pick unique port number instead of `2222` which is just example. Don't forget to change username to yours. Also you can change sleepy to sneezy.
- Again, go to Remote Explorer.
- Click on the `+` button to add a new SSH target.
- This time, type `ssh username@localhost -p 2222` for `sleepy` .
- Save the configuration and click on the "**Connect to Host in New Window**" button.

By following these steps, you should now be connected to your `sleepy`  or `sneezy`  remote server through `earth` .

However this manual work is needed every time (VSCode cache it for sometime but will be deleted after sometime) So you can use you machine local ssh config file to store connection information ( You will not have to remember the port you used last time as you can use the same one now)

You can edit you local `.ssh/config` file which location will depend on your OS. For MacOS it will inside your home directory. i.e `/Users/<username>/.ssh/config` so you can use some thing like `vim` to edit it. One trick that works in both Mac and Linux is to refer to your local user ssh config by `~/.ssh/config` / Generally speaking `~` will be interpreted in your shell environment as you user home directory.

| **OS**  | **SSH Config Location**           |
| ------- | --------------------------------- |
| MacOS   | `/Users/<username>/.ssh/config`   |
| Linux   | `/home/<username>/.ssh/config`    |
| Windows | `C:\Users\<username>\.ssh\config` |

Inside your file you can put the following:

```other
Host sleepy-earth
	User <username>
	HostName sleepy.geop.uc.edu
	Port 22
	LocalForward 8967 localhost:8967
	ForwardAgent yes
	ForwardX11 yes
	ForwardX11Trusted yes
	ProxyJump <username>@earth.phy.uc.edu:22
	ServerAliveInterval 600
	ServerAliveCountMax 240
```

This will create permanent ssh configuration for your manual connection that you did before.

**Bonus**: In your terminal (Any terminal on your machine) you can write:

```other
ssh sleepy-earth
```

And it will login you sleepy at the end (after entering both `earth` and `sleepy` password)

But now when you go to VSCode Remote Explorer you will find sleepy-earth option which when you click will do all the steps necessary to login to sleepy (it will give you prompt to enter passwords).

You can do the same with `sneezy` as

```other
Host sneezy-earth
	User <username>
	HostName sneezy.geop.uc.edu
	Port 22
	LocalForward 8967 localhost:8967
	ForwardAgent yes
	ForwardX11 yes
	ForwardX11Trusted yes
	ProxyJump <username>@earth.phy.uc.edu:22
	ServerAliveInterval 600
	ServerAliveCountMax 240
```

**Hint**: Don’t forget to choose unique port instead of `8967` because port conflicts will cause problems in connections.

#### Config Parameters

| **Line**                                   | **Description**                                                                                                                                          |
| ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Host sleepy-earth`                        | Defines an alias `sleepy-earth` for the SSH connection. This allows you to connect using `ssh sleepy-earth` instead of using the full connection string. |
| `User <username>`                          | Specifies the username to use when logging into the `sleepy` server. Replace `<username>` with your actual username.                                     |
| `HostName sleepy.geop.uc.edu`              | Sets the hostname or IP address of the remote server you're connecting to. Here, it's `sleepy.geop.uc.edu`.                                              |
| `Port 22`                                  | Defines the port number on which to connect to the remote server. The default SSH port is 22.                                                            |
| `LocalForward 8967 localhost:8967`         | Forwards the local port 8967 to `localhost:8967` on the remote machine, enabling port forwarding.                                                        |
| `ForwardAgent yes`                         | Allows the SSH authentication agent (often `ssh-agent`) to forward authentication data to the remote machine.                                            |
| `ForwardX11 yes`                           | Enables X11 forwarding, allowing you to run graphical applications over the SSH connection.                                                              |
| `ForwardX11Trusted yes`                    | Allows untrusted X11 forwarding, which provides certain security benefits but should be used cautiously.                                                 |
| `ProxyJump <username>@earth.phy.uc.edu:22` | Specifies that the SSH connection should be tunneled through the server at `earth.phy.uc.edu` with the given `<username>`.                               |
| `ServerAliveInterval 600`                  | Sets the interval, in seconds, at which empty SSH packets are sent to keep the connection alive. Here, it's set to 10 minutes (600 seconds).             |
| `ServerAliveCountMax 240`                  | Specifies the number of server alive messages that may be sent without any response from the server before disconnecting.                                |

There are some optional parameters that I find nice to have but not necessary like `ServerAliveInterval` and `ServerAliveCountMax`  so feel free to remove or edit their values. But keep everything else the same (Other than the `port number` and `username` which should always be unique to you). You can also change the alias from `sleepy-earth` to anything you want.

Hint: The first time you connect (and when VSCode does update) it will take a couple of minutes because actually VSCode is being installed on the remote server (in your local directory there)

## Running Jupyter Notebooks in VSCode

### Requirements

1. Install the `Jupyter` extension in your remote VSCode environment.
You should download the extension after you login in so that it is installed on VSCode remote not your local VSCode

### Differences from Jupyter Notebook Server

1. **Resource Utilization**: Running Jupyter within VSCode can be less resource-intensive, as you don't need to run a full web server.
2. **Integrated Environment**: VSCode offers better integration with Git, terminals, and other extensions.
Extension suggestions: `Python` `IntelliCode` `PDF Viewer` `Pylance` `ROOT File Viewer`
3. **Debugging**: Native debugging capabilities are stronger in VSCode. This is extensive and you should look for debugging in VSCode.
4. **File System Access**: Easier to navigate and manipulate remote files directly. You will navigate them like in your local machine (And copy paste, download and upload doesn't take the life from you as in using `scp` )

### Usage

1. Open a new .ipynb file or an existing Jupyter notebook file.
2. The notebook interface should appear.
3. If not, select the Python interpreter where Jupyter package is installed.
4. You can now run cells by clicking the "Run Cell" button or using the `Shift + Enter` shortcut.

To use the terminal in the remote server, simply click on the "Terminal" tab in VSCode, which should open a new terminal window connected to your remote machine. You can then execute shell commands as if you were SSH'd into the server.

