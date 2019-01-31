# Tutorial

## Course VM

In order to make it easy for you to compile the homework locally on your laptop,
we have created a script that will generate an
[Ubuntu](https://www.ubuntu.com/) based
[Virtual Machine (VM)](https://en.wikipedia.org/wiki/Virtual_machine)
that contains everything you need.

You can use the VM in order to develop your code and test if it works.
Please not though, that you should **not use the VM for performance tests**.
We are going to evaluate your homeworks for how they perform on the Cori
supercomputer. The VM on your local laptop will have different performance
characteristics compared to the super computer.
Put a different way: The fastest program on the VM may not be the fastest
program on the supercomputer!

### Building the VM

In order to provide you with a reproducible
[VM](https://en.wikipedia.org/wiki/Virtual_machine)
we use a tool called [Vagrant](https://en.wikipedia.org/wiki/Vagrant_(software)).

Vagrant allows us to write a [provisioning script](https://www.vagrantup.com/docs/vagrantfile/)
that contains all command needed to configure the
[Ubuntu 18.04](http://releases.ubuntu.com/18.04/)
base image. This way we can easily regenerate the VM.
The script can also be used as a guide for those of you who want
to install the required packets on their native Linux installation.

In order to build the VM you need to
[install Vagrant](https://www.vagrantup.com/docs/installation/)
as well as the [VirtualBox Hypervisor](https://www.virtualbox.org/wiki/Downloads).
Vagrant itself does not host your VM, it only serves as a scriptable
interface to the actual Hypervisor.

Once you have Vagrant and VirtualBox installed, you need to download
the base image, download our repository containing the `Vagrantfile` and
execute the `vagrant up` command in the same directory as the `Vagrantfile`:

```.sh
git clone https://github.com/ekiwi/cs267.git
cd cs267
vagrant box add ubuntu/bionic64
vagrant up
```

### Using the VM

Once the VM is built and launched, you can use `vagrant ssh` to login.
The `hw` folder will mirror the content of the `cs267` folder on your
machine. Thus you could use a local (graphical) editor on your machine to
edit the local file and all changes will also be available on your VM
where you can compile them.
_Note:_ If you are a Windows user, make sure that your local code editor
supports [UNIX line endings](https://en.wikipedia.org/wiki/Newline#Representation).

To shutdown the VM type `sudo shutdown` in the VM shell (the one that you
got after using `vagrant ssh`). To start the VM again you can use
`vagrant up` again.

### Tools on the VM

The VM has `nano` and `vim` installed as text editors.
It also features modern versions of
`grep` (`rg` aka [RipGrep](https://github.com/BurntSushi/ripgrep)) and
`cat` (`bat` [bat](https://github.com/sharkdp/bat)).
Their basic usage is similar to the tools they are replacing, but
the are much faster and support code syntax highlighting.
Of cause `grep` and `cat` are also provided if you want to stick with
the classics.

## Logging Into Cori

**TODO**: setup 2FA, how do you create the 24h key? maybe how to install SSH client on Windows/macOS?

## Command Line

Once you log into Cori, you will be presented with a
[Bash shell](https://en.wikipedia.org/wiki/Bash_(Unix_shell)).
While Bash features a full scripting language - in this class - we are
going to use it only to launch simple commands.

Some important commands are:

```
ls                          # list files in currect directory
ls *.txt                    # list files in currect directory that end in .txt
cd [DIRNAME]                # change current directory
cd ~                        # change to your home directory
cd ..                       # go up one directory
cat [FILENAME]              # print file content
cat [FILENAME] | less       # print file content, scroll with arrows, quit with q
grep "needle" [FILENAME]    # searches for "needle" in file
man [COMMAND]               # display the manual for COMMAND, use q to quit
exit                        # close shell
```

You can go back to the last used command by using `↑` key.
Press `Enter` if you want to use the command, use `Backspace` to edit the
command before using it.
Use `Ctrl+R` to search through your command history.
Use `cat ~/.bash_history | less` to view your complete command history.

## Editor

One of the easiest ways to implement your homework is to directly change
the code on the server.
For this you need to use a command line editor like nano or vim.

For beginners we recommend taking your first steps with [nano](https://en.wikipedia.org/wiki/GNU_nano).
You can use it on Cori like this:

```.sh
[demmel@cori10 cs267_hw1_2019]$ module load nano
[demmel@cori10 cs267_hw1_2019]$ nano dgemm-blocked.c
```

Use `Ctrl+X` to exit.

For a more complete code editor try [vim](https://en.wikipedia.org/wiki/Vim_(text_editor))
which is loaded by default:

```.sh
[demmel@cori10 cs267_hw1_2019]$ vim dgemm-blocked.c
```

Use `Esc` and `:q` to exit.
(`:q!` if you want to discard changes).
Try out the [interactive vim tutorial](https://www.openvim.com/) to learn more.

## SSHFS

SSHFS is a utility that allows you to mount remote file systems to your local
system using SSH.  This has the advantage that you can use your favorite editor
such as Atom, Gnome Builder, Sublime Text, or Bloodshed Dev-C++ to edit files
on a remote file system.  To use SSHFS just install `sshfs` using your package
manager and then mount the remote file system to your local system

```Bash
[xiii@shini ~]$ mkdir cori
[xiii@shini ~]$ sshfs brock@cori.nersc.gov: cori
Password + OTP:
[xiii@shini ~]$ cd cori
[xiii@shini cori]$ ls
intel  local  src
```

You can now direct your local editor to the files you want to edit in the
new mounted directory.

## Compilers

Compilers on Cori are selected using a "programming environment" module.  No matter
which compiler you use, you always compile on Cori using the `CC` (for C++) or `cc`
commands, which use special wrappers that invoke the compilers for you with some
specially tuned flags enabled (such as `-march=native`).

### Intel

The Intel compilers are loaded by default on Cori.  They are available in the module `PrgEnv-intel`.

### GNU

You can switch to using the GNU compiler by changing the programming environment module
mentioned earlier.  To do this, you can use the command `module swap PrgEnv-intel PrgEnv-gnu`.
Now, when you invoke `CC` or `cc`, you will be using the GNU compilers.

## Compiler Explorer

You can examine the assembly produced for your code by using the `-S` flag in your compiler.

```Bash
[demmel@blasboss ~] cc -S hello.c -o hello.s
[demmel@blasboss ~]$ head hello.s
        .file   "hello.c"
        .text
        .section        .rodata
.LC0:
        .string "Hi, fam!"
```

While this can be useful, it's often cumbersome to constantly edit a sourcefile and then
have to recompile to view the assembly.  There's a nice tool called the
[GodBolt Compiler Explorer](https://godbolt.org/) that let's you interactively edit
code and view the assembly interactively as you optimize.  It also has a couple of
nice features that you won't find just using your local C/C++ compiler: it will color
code lines of code to visually match them up with the corresponding lines of assembly,
and it will use some advanced symbol lookup techniques to place more human-readable names
for your functions and variables in the assembly output.

## C/C++ Debugging

lel

### address sanitizer

lel

### other sanitizers

### gdb

lel

## make

lelel


## C/C++

Types pls

## Common pitfalls?

Pits
Using Java, emacs
