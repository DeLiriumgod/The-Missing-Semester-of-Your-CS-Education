### 1/13 课程概览与 shell

#### 主题1：The Shell

为了充分利用计算机的能力，我们不得不回到最根本的方式，使用文字接口：Shell

##### 使用 shell

> 输入命令`data` 执行程序，也可以带参数如`echo hello`

那么 shell 是去哪里寻找 `date` 或 `echo` 的呢？当我们执行 `echo` 命令时，shell了解到需要执行 `echo` 这个程序，随后它便会在`$PATH`中搜索由`:`所分割的一系列目录，基于名字搜索该程序。我们也可以通过直接指定绝对路径来执行该程序。

##### 在shell中导航

shell 中的路径通过`/`分割，路径`/`表示根目录，若一个路径以`/`开头，那么他是一个_绝对路径_，其他的均为_相对路径_，在路径中`.` 表示的是当前目录，而 `..` 表示上级目录。

> 输入命令`pwd`可以获得当前目录

> 输入命令`cd`可以切换目录

> 输入命令`ls`可以查看指定目录下包含哪些文件

大多数的命令接受标记和选项（带有值的标记），它们以`-` 开头，并可以改变程序的行为

`-h`或`-help`可以打印帮助信息

`-l`可以打印出更加详细地列出目录下文件或文件夹的信息

```shell
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

首先，本行第一个字符`d` 表示 `missing` 是一个目录。然后接下来的九个字符，每三个字符构成一组。 (`rwx`)(`r-x`)(`r-x`)它们分别代表了文件所有者(`missing`)，用户组 (`users`) 以及其他所有人具有的权限。

其中 :为了修改文件内容，用户必须对文件具备写权限`w`；为了列出它的包含的内容，用户必须对该文件夹具备读权限（`r`）；`-`表示该用户不具备相应的权限；为了进入某个文件夹，用户需要具备该文件夹以及其父文件夹的“搜索”权限（以“可执行”：`x`）权限表示。

> 输入命令`mv`可以重命名或移动文件

> 输入命令`cp`可以拷贝文件

> 输入命令`mkdir`可以新建文件夹

> `man`程序接受一个程序名作为参数，然后将它的文档（用户手册）展现

##### 在程序间创建连接

在shell中，程序有两个主要的“流”：他们的输入流和输出流。我们也可以重定向这些流。

最简单的重定向是 `> file` 和 `< file`。这两个命令可以将程序的输入输出流分别重定向到文件。`>>`表示追加输入

使用管道（ *pipes* ），我们能够更好的利用文件重定向。`|`操作符允许我们将一个程序的输出和另外一个程序的输入连接起来

> 命令`tail -nl`可以获得输入流的最后一行

```shell
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
```

##### 一个功能全面又强大的工具

> `sudo` 命令可以su（super user 或 root的简写）的身份do一些事情

```shell
$ cd /sys/class/backlight/thinkpad_screen
$ sudo echo 3 > brightness
```

关于shell，有件事我们必须要知道。`|`、`>`、和 `<` 是通过shell执行的，而不是被各个程序单独执行。 `echo` 等程序并不知道`|`的存在，可修改为：

```shell
$ echo 3 | sudo tee brightness
```

##### 课后练习

1. 在 `/tmp` 下新建一个名为 `missing` 的文件夹。

    ```shell
    mkdir missing
    ```

    

2. 用 `man` 查看程序 `touch` 的使用手册。

    ```shell
    man touch
    ```

    

3. 用 `touch` 在 `missing` 文件夹中新建一个叫 `semester` 的文件。

    ```shell
    touch semester
    ```

    

4. 将以下内容一行一行地写入`semester`文件：

    ```shell
     #!/bin/sh
     curl --head --silent https://missing.csail.mit.edu
    ```

    第一行可能有点棘手， `#` 在Bash中表示注释，而 `!` 即使被双引号（`"`）包裹也具有特殊的含义。 单引号（`'`）则不一样，此处利用这一点解决输入问题。更多信息请参考 [Bash quoting手册](https://www.gnu.org/software/bash/manual/html_node/Quoting.html)

    ```shell
    echo \#'!'/bin/sh > semester
    echo curl --head --silent https://missing.csail.mit.edu >> semester
    ```

    

5. 尝试执行这个文件。例如，将该脚本的路径（`./semester`）输入到您的shell中并回车。如果程序无法执行，请使用 `ls`命令来获取信息并理解其不能执行的原因。

    > 提示权限不够
    >
    > 执行`ls -l`命令
    >
    > 显示`-rw-r--r-- ...`显然没有可执行`x`权限

6. 查看 `chmod` 的手册(例如，使用`man chmod`命令)

    ```shell
    man chmod
    ```

    

7. 使用 `chmod` 命令改变权限，使 `./semester` 能够成功执行，不要使用`sh semester`来执行该程序。您的shell是如何知晓这个文件需要使用`sh`来解析呢？更多信息请参考：[shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))

    ```shell
    chmod ugo+x semester
    ./semester
    ```

    

8. 使用 `|` 和 `>` ，将 `semester` 文件输出的最后更改日期信息，写入根目录下的 `last-modified.txt` 的文件中

    ```shell
    ./semester | grep -i "last-modified" > /home/last-modified.txt
    ```

9. 写一段命令来从 `/sys` 中获取笔记本的电量信息，或者台式机CPU的温度。注意：macOS并没有sysfs，所以mac用户可以跳过这一题

    ```shell
    cat /sys/class/power_supply/battery/capacity
    ```


