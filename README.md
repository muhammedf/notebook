# SSH (Windows10)


## Generating SSH Key
- Run `ssh-keygen` in command line.
- Now it expects a file name input. Enter an arbitrary one. Let's assume it is 'mykey'.
- It will ask for passphrase then, to skip that just click enter.
- One enter too.

Now `mykey` and `mykey.pub` files are created in the directory where command line is currently running. Let's assume the directory is `~/.ssh`.

`mykey` file contains private key and must not be shared anyone. We will be sharing public key which `mykey.pub` file contains.


## Introducing SSH Keys To Agent
- Run `ssh-add ~/.ssh/mykey` in command line.

To see if it did work, you can run `ssh-add -l` command. This shows all ssh keys added.


## Starting SSH Agent
- Run `Set-Service ssh-agent -StartupType Automatic` in ***elevated*** PowerShell.

This command ensures that ssh-agent starts when machine starts.

- Run `Start-Service ssh-agent` in powershell.

## Connecting a Remote Machine with SSH
- We can use `ssh username@ipaddress` command to connect a remote server through ssh. Let's assume username is `root` and ipaddress is `123.123.123.123`. Exact command will be `ssh root@123.123.123.123`.
- It will ask password unless we configured ssh before. At this point we have to be knowing this information. I am assuming that we know it and write it down there.
- Now we are in. We are gonna edit the file which its path is `~/.ssh/authorized_keys`. If file is not existed, you can create. This file stores authorized ssh keys. Im editing the file wih `nano`, but does not matter. Any text editor that available will serve the purpose. My command is `nano ~/.ssh/authorized_keys`.
- Now we will be copying our `mykey.pub` file content and pasting it into the nano. After that, save and close the nano.
- Now to try it, type exit and click enter.
- Again run `ssh root@123.123.123.123` command. Unless we missed a step, you have to be in without the remote machine asking the password.

## Git
I shared my public key with github but my git commands fail. (Permission denied (publickey).) Reason of that is in windows, git uses its own ssh program and we just have to change that.
- In the global gitconfig file which its path is `~/.gitconfig`, there is a `sshCommand` variable that is currently set to a path inside of git program. We are gonna set it as `C:/Windows/System32/OpenSSH/ssh.exe`
- Then we might need to restart our machine.

Now git commands will not fail. At least not the permission related ones. Not if you dont have permission to the said repo.

## SourceTree
If you were already using SourceTree with OpenSSH client before making the configurations above you might encounter some problems. First of all SourceTree is using git's embedded ssh client too and it doesnt change when we made the change to `sshCommand` variable. So it thinks ssh-agent is not running even though we started it and starts another instance. To fix this, we are gonna do two or three things.
- Under the `Tools` menu select `Options`.
- Go to `Git` tab and find the `Git Version` section below. Click `System` button in there. If the button is disabled, it means it is already selected.
- Go to `General` tab and find the `SSH Client Configuration` section.
    - Change the value of `SSH Client` as `PuTTY / Plink`.
    - Uncheck the `Automatically start SSH agent when Sourcetree opens` checkbox.

By doing these, we ensure that SourceTree will use global installation of git and will not try to start ssh-agent again.