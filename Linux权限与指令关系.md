#### 权限与指令关系

###### 1.让用户能进入某个目录成为【可工作目录】的基本权限

可以使用指令：cd等

目录所需全向：用户对这个目录至少具有X的权限

额外需求：如个用户现在这目录内利用ls查阅文件名，则用户此目录还需R 权限

###### 2.用户在某个目录内读取一个文件的基本权利

可以使用指令：cat,tac,tail,head,more,less等

目录所需全向：用户对这个目录至少需要具有X权限

额外需求：使用者对这文件至少需要具有R权限才能读取

###### 3.让用户可以修改一个文件的基本权限

可以使用指令：文本编辑指令：nano,vi等指令

目录所需全向：用户对该文件所在目录至少有X权限

额外需求：使用者对该文件至少要R,W权限

###### 4.让一个使用者可以建立一个文件的基本权限

可以使用指令：touch，mkdir等指令

目录所需全向：用户对需要建立所在的文件目录具有X，w权限，重点在W权限。

###### 5.让用户进入某目录并执行该目录下的某个指令的基本权限

目录使用指令：用户对该目录至少具有X权限

文件所需全向：使用者在对该文件至少有X权限

#### 重点总结

绝对路径/相对路径

特殊目录：. , .. , - , ~ ,~account

与目录相关的指令：cd,mkdir,rmdir,pwd等重要指令

rmdir仅能删除空目录，要删除非空目录需使用 rm -r 指令

用户能使用的指令是依据PATH环境变量所规定的目录区搜索的执行文件来执行该指令

ls可以检视文件的属性，尤其-d,-a,-l等选项特别重要！

文件的复制、删除、移动可以分别使用：cp,rm,mv等指令来操作

检查文件的内容（读文件）可使用指令有：cat，tac, nl, more, less, head, tail,od等

cat -n 与nl 均可显示行号，默认情况下，对空白行是不是编号并不相同

touch的目的在修改文件时间参数，但也可用来建立空文件

一个文件记录的时间参数有三种：access time(atime,最新访问时间),  status time(ctime,最近状态修改时间)， modification time(mtime,最近修改时间)   ， 默认显示的时间是mtime

除了传统的RWX权限之外，在ext2/ext3/ext4/xfs文件系统中，还可以使用chattr(设定）与lsattr(显示）隐藏属性，常见的包括只能新增数据的+a 与完全不能更动文件的+i 属性。

新建文件/木目录时，新文件的预设权限使用umask来规范，使用减法规则，修改默认权限：umask 027

文件具有SUID的特殊权限时，代表当用户执行自已拥有X权限的binary程序时，在执行过程中该用户会暂时拥有该程序拥有这的权限，例如修改个人密码/etc/passwd

目录具有SGID的特殊权限时，代表用户在这个目录地下新建的文件之群组都会拥有该文件的权限，如小组在同一个目录下开发软件

目录具有SBIT的特殊权限时，代表该目录下用户建立的文件只有自己与root能够删除

观察文件类型可有file指令观察

搜索指令的完整文件名可用which或type,这两个指令都是通过PATH环境变量来搜索文件名的

搜寻文件完整的文件名可使用whereis找特定的目录或locate到数据库去搜寻，使用shereis时注意只是在特定的目录搜寻，使用locate的数据库跟新不及时，若无法查询新文档，可手动更新updatedb数据库

利用find可加入许多选项来直接查询文件系统，以获得自己想要知道的文件。

增加新的用户/群组：useradd username;groupadd groupname;

```bash
#groupadd project
#useradd -G projetct alex       <<创建用户并添加到project群组中
```

## Linux磁盘与文件系统管理

系统管理员最重要的任务之一是管理好自己的磁盘文件系统，每个分区槽不可太大也不能太小，太大造成磁盘容量的浪费，太小则会产生文件无法存储的困境；（filesystem,inode,block)

### 认识Linux文件系统

Linux最传统磁盘文件系统：ext2

文件系统是建立在磁盘上面，需了解磁盘物理组成

#### 磁盘组成与分区简介

##### 整颗磁盘组成：

圆形的磁盘盘（主要记录数据部分）

机械手臂，与在机械手臂上从磁盘读取头（可擦写磁盘盘上的数据）

主轴马达，可以转动磁盘盘，让机械手臂的读取头在磁盘盘上读取数据；



##### 主要部件磁盘盘上的物理组成：

扇区（sector)为最小的物理存储单位，且依据磁盘的设计的不同，目前主要有521bytes与4K两中格式

将扇区（sector）组成一个圈，那就是磁柱（cylinder)

早期的分区主要是以磁柱，现在的分区通常使用扇区为最小分区单位（每个分区都有其编号）

磁盘分区表主要有两种格式，一种是限制比较多的MBR分区表，一种是较新且限制比较少的GPT分区表

MBR分区表中，第一个扇区最重要，里面有：主要开机区（Master boot record ,MBR）及分区表（partition table),其中MBR占有446biytes,而parition table则占有64bytes

GPT分区表除了分区数量扩充比较多之外，支持的磁盘容量也可以超过2TB



实体磁盘：/dev/sd[a-p]        分区槽的文件名：/dev/sda[1-128]

虚拟机磁盘：/dev/vd[a-p]  

软件磁盘阵列：/dev/md[0-128]

LVM：/dev/VGNAME/LVNAME



##### 磁盘分区完毕后需要格式化（format) -------操作系统格式化为自己该操作系统能够使用的文件系统

（以为每个操作系统所设定的文件属性/权限格式并不相同，尽管放置相同的数据内容，而磁盘分区有对应的文件属性/权限格式，需要格式以成为操作系统能够使用的【文件系统格式（filesystem)】）

例如：Windows 98  操作系统使用FAT(FAT16)文件系统； Windows 2000以后使用NTFS文件系统

Linux的正统文件系统为ext2(Linux second extended file system ,ext2) 使用默认情况下，Windows操作系统不会认识Linux的EXT2,对于Linux同理。

##### 磁盘与文件系统的应用：

传统：一个分区槽只能被格式化成为一个文件系统（filesystem~partition)

现在：一个分区槽格式可划分为多个文件系统（LVM、软件磁盘阵列），也可将多个分区槽合并成一个文件系统

（通常我们称：一个被挂载的数据为一个文件系统而不是一个分区槽）



##### 文件系统如何运作：

与操作系统文件数据有关，文件数据=实际内容+文件权限/属性，文件系统通常会将这两部分的数据分别在不同的区块中，例如Linux权限和属性放置在inode中，而实际数据这放置在data block区块中，除此之外还有一个超级区块（superblock)会记录整个文件系统的整体信息，包括inode与block的总量、使用量、剩余量等

每个inode与block都有编号

superblock:记录此filesystem的整体信息，包括inode/block的总量、使用量、剩余量，已经文件系统的格式与相关信息等

inode:记录文件的属性、权限，一个文件占用一个inode,同时记录此文件的数据所在的block编码

block：时间记录文件的内容，若文件太大时，会占用多个block

工作原理：每个inode与block都有编码，而每个文件都会占有一个inode,inode内则有文件数据放置的block编码，因此操作系统如果能找到文件的inode的话，那么自然就知道这个文件所放置的block编码，当然就能过找到该文件的实际位置，读出该文件的实际数据，这样就很有效率，Linux也正是这样工作的（文件系统先格式化出supersystem,inode,block区块）

例如文件A的数据存放在inode4号中（权限，属性，block区块编码），inode记录文件数据实际存放数据为2，7，13，15这四个block编码，操作系统读取A文件的block的顺序也为2,7,13,15（该数据存取方式：索引式文件系统【indexed allocation】）ext2文件系统采用的方式

![](F:\Linux截图\inode与block工作原理.PNG)

FAT文件系统（闪存、Windows98等）

没有inode的存在，所以FAT没有办法将文件的所以block在一起开始就读取出来，采用单链表的形式读取文件，前一个block记录着下一个block的编码，如图的读取顺序为：1->7->4->15，（产生的问题可能导致文件的block太过于分散）

![](F:\Linux截图\FAT文件系统格式.PNG)

所以FAT文件系统会存在【碎片整理】：原因就是FAT文件系统写入的文件block太过于离散，此时文件读取需要跨过较大的block区间，导致文件读取速度减慢。可以通过碎片整理将通一个文件所属的blocks汇整到相对集中的block区域中，这样提示文件的读取容易且速度块。

ext2文件系统格式基本不存在上诉的block的整理问题，尽管block离散，但文件才用inode整体读方式，所以读取文件很快。

#### Linux的ext2文件系统（inode）

inode记录了文件的权限和属性以及block编码集

而文件系统一开始就将inode与block规划好，除非重新格式化（或者利用热size2fs等指令变更文件系统大小），否则inode、block、superblock固定后就不在变动。（存在如果文件系统高达数百GB时，那么将所有的inode与block放置在一起就很不明智的做法，inode与block数量太庞大，不容易管理）

所有ext2文件系统在格式化的时候基本就分区为多个区块群组（block group），每个区块群组都有独立的inode/block/supreblock系统

![ext2系统图](F:\Linux截图\ext2系统图.PNG)

整体规划中，文件系统最前面有一个启动扇区（boot sector），这个启动扇区可以安装开机管理程序，非常重要的系统设计，因为这样我们就能够将不同的开机管理程序安装到别的文件系统最前端，而不用覆盖整可磁盘唯一的**MBR**,这样才能够制作出多重引导的环境。



##### data block（数据区块）

data block用来存放文件内容数据的地方，ext2文件系统支持的block大小有1K，2K及4K三种；（格式化时block大小和每人block编码固定，以便inode的记录，因为block大小差异，导致该文件系统支持的最大磁盘容量与最大单一文件容量并不相同![ext2不同block大小导致文件系统支持的最大磁盘容量](F:\Linux截图\ext2不同block大小导致文件系统支持的最大磁盘容量.PNG)

ext2文件系统的block限制：

原则上，**block的大小与数量**在格式化完就不能再改变了（除非重新格式化）

每个block内有且只能放置一个文件的数据内容，所以如个文件大于block的大小，则文件会占用多个block数量，若文件小于block，则该block的剩余容量不能被其他文件所利用（磁盘空间因此产生浪费）

每个文件占有整数个block，文件时间数据大小=<文件占有block总容量大小

所以block的大小划分都有利弊；

inode table（inode表格）

inode存放数据（一个文件对于一个inode)：

该文件的存取模式（read,write,excute)

该文件的拥有者与群众（owner，group)

该文件的大小（容量）

问文件建立或状态的是时间（ctime,mtime,atime)

定义文件特性的旗标（flag），如SUID，GUID,SBIT

该文件真正内容的指向（pointer）集

**inode的梳理与大小也是在格式化的时候已经固定了**

每个inode大小均固定为128bytes(新的ext4与xfs可设定到256bytes)

每个文件都仅会占有一个inode而已

因此文件系统能够建立的文件数据与inode的数量有关

系统读取文件时需要找到对应的inode，并分析inode所记录的权限与用户是否符合，若符合才能够开始实际的文件实际内容block操作。

> *（记录一个block编码要花费4bytes)     一个inode（128bytes)拥有12个直接、一个间接、一个双间接和一个三间接用以记录inode对应文件的block编码）*

![inode结构示意图](F:\Linux截图\inode结构示意图.PNG)

计算如图的一个inode最多能指定多少个block开存储文件实际内容（block大小假设为1k)

解：12直接指向：12*1K=12K

1间接：1*1k/4B=256K

1双间接：256*256=65536K

1三间接：256*65536=16777216K

总额=12K+256+65536+16777216K=16GB

##### superblock(超级区块)

> superblock是记录整个filesystem相关信息的地方，没有superblock，就没有filesystem。

superblock记录的信息：

block和inode的总量

未使用和已使用的inode,block数量

block与inode的大小（block为1,2,4K,inode为128bytes或256bytes)

filesystem的挂载时间、最近一次写入数据的时间、最近一次检验磁盘(fsck)的时间等文件系统相关信息

一个valid bit数值，若此文件系统被挂载，则valid bit为0，若未被挂载则valid bit为1.

一般情况，superblock大小为1024bytes

> (每个block group都有可能包含有superblock)，但文件系统应该只有一个superblock，这样做的原因是在平时文件系统正常的情况下使用第一个block group中的superblock为主，由于superblock很重要，所以第一个block group出现问题时，对其他的block group的superblock做为备份进行救援文件系统。

##### Filesystem Description（文件系统描述说明）

这个区段可以描述每个block group的开始与结束的block编码，以及说明每个区段（superblock，bitmap,inodemap,data block)分别介于那个block编码之间，可通过dumpe2fs观察

##### block bitmap（block 对照表）

功能：通过block bitmap可知道文件系统中那些block是否被使用，添加新文件时，需使用未被使用过的block，通过block bitmap实现找到空的block，并将新文件写入空的block中

记录文件系统中每个block的使用状态，【使用】/【未使用】；删除文件时修改block bitmap中原文件对应的block状态【使用】-->【未使用】

##### inode bitmap（inode 对照表）

与block bitmap功能类似，只是记录的是inode的使用状态【使用】/【未使用】

删除文件时:修改原文件对应的inode 对应的inode bitmap的使用状态：【使用】——>【未使用】

建立新文件时：同理在inode bitmap中获取一个状态为【未使用】——>【使用】，同时在实际对应的inode中修改对应的新文件信息

##### dumpe2fs:查询ext家族superblock信息指令

格式：#dumpe2fs [-bh] 装置文件名

参数与参数：

-b :列出保留为坏轨的部分（一般用不到）

-h :列出supeblock的数据，不会列出其他区段内容

#### 与目录树的关系

（目录的内容在记录文件名）

##### 目录

> Linux建立一个目录时：文件系统会分配一个inode和至少一块block给该目录。inode记录该目录的相关权限和属性，并可记录分配到的block编码（12直接、1间接、1双间接、1三间接）；而分目录分配到的block则记录着个目录下面的文件名和这些文件对应的inode编码；（注：inode也有其对应的编码1inode占用128bytes）![目录文件占有的block数据内容](F:\Linux截图\目录文件占有的block数据内容.PNG)

##### 文件

当我们在Linux下建立一般文件时，文件系统会分配一个inode与相对应与于该文件大小的block数量给该文件。block大小通常为1K，2K，4K；若建立一个100kbytes大小文件，那么该文件将获得25个block（假设1block设置为4K)

##### 目录树读取

inode本身不记录文件名，文件名的记录是在目录的block中。

> 使用才会有【新增，删除，更名与目录的w权限有关】，w的权限就是对文件对应的实际内容block的修改，而文件名是记录在目录的block中，因此当我们要读取某个文件时，就务必会经过目录的inode和block，然后才能读取到待读文件的inode编码

（对其某一个文件只能通过获取到其对应的inode才能读取，而通常我们只知道文件名而不知道文件的inode编码，而文件名与其inode关系存储在目录文件的block中）

![目录与inode的关系演示](F:\Linux截图\目录与inode的关系演示.PNG)

目录与inode的在文件操作时的关系演示图如上：文件名——>inode——>block——>文件名——>inode....

知道找到目标文件（在到达每一步inode时需要查找用户的对应文件权限是否满足，满足允许继续操作，不满足拒绝操作）

##### filesystem大小与磁盘读取效能

虽然ext对于文件数据离散问题不是很突出，但如果ext文件系统的一个文件的实际数据内容的block太过于离散的话，也会存在读取效能低下的情况(因为在读取该文件时，磁盘读取头还是会在整个文件系统中跨度较大的来回读取数据)；

解决办法：

> 将整个filesystem内的数据全部复制出来，并重新格式化filesystem，再讲数据全部复制回来。

#### ext2/ext3/ext4文件的存取与日志式文件系统的功能

对于新建一个文件或目录时，需要用到inode bitmap及block bitmap；文件系统的行为：

> 1.先确定用户对于欲增加的文件的目录是否具有x和w权限，若有才新增；
>
> 2.跟进inode bitmap找到没有使用的inode编码，并找到对应编码的inode写入新文件的权限、属性。
>
> 3.跟进block bitmap找到未使用的block编码，并将新文件的实际数据写入block中，且更新inode的block指向数据
>
> 4.将刚刚写入的inode和block数据同步更新到inode bitmap和block bitmap，并更新superblock。

一般情况：

将inode table 与data table称为数据存储区

将superblock、inode bitmap和block bitmap称为中介资料（metadata)

##### 数据的不一致（inconsistent)状态

在写入数据是系统出现意外时，数据仅有写入inode table和data table中，而没有有更新到metadata中，这样产生了metadata的内容与实际数据存放区产生不一致（inconsistent)的情况；

解决办法：早期采用寄于superblock中记录的valid(是否有挂载)与filesystem state（clean与否）进行强制判断是否产生数据一致性的检查。但这样做很费时，因为需要搜索整个filesystem。

日志式文件系统（journaling filesystem）

在filesystem中专门规划一个区块，用于专门记录修订的文件时的步骤：

1.预备：当系统要写入一个文件时，会先在日志记录区块中记录某个文件准备写入的信息

2.实际写入：开始写入文件的权限与数据；开始更新metadata的数据

3.结束：完成数据与与metadata的更新后，在日志记录当中完成该文件的记录；

##### Linux文件系统的运作

数据都要加载到内存后CPU才能对该数据进行处理，由于存在磁盘写入速度与CPU读取速度差异较大

解决办法：异步处理（asynchronously)方式

工作原理：当系统在加载一个文件到内存后，如果该文件没有被更改过，则在内存区段的文件数据会被设为干净（clean），但如果内存中的文件数据被修改过（例如使用naono、vi去编辑过这个文件）此时该内存中的数据会被设定为脏的（Dirty).此时所有的动作都还在内存中执行，没有写入到磁盘中！系统会不定时的将内存中设定为（dirty)的数据写回到磁盘中，以保持磁盘与内存数据的一致性。也可强制使用 sync 指令来手动强迫写入到磁盘中。

内存速度比磁盘快很多；将常用文件放置在内存中，会大大增加系统性能，因此Linux系统上面文件系统与内存有非常大关系：

系统会将常用文件数据放置到主存储器的缓存区，以加速文件系统的读写，所以Linux的物理内存最后都会被用光，这是正常情况

可手动强制使用sync来强迫内存设定的dirty的文件写回到磁盘中

若正常关机时。关机指令会主动呼叫sync来将内存的数据写回到磁盘中

若不正常关机（如跳电、当机或其他不明原因），由于数据尚未写回磁盘内，因此重启后可能会花很多时间来进行磁盘检验，甚至可能导致文件系统的损坏（非磁盘损坏）

##### 挂载点的意义（mount point）

每个filesystem都有独立的inode/block/superblock/inode table/block table/inode bitmap/block bitmap

文件系统只有链接到目录树才能被使用，该链接称为挂载，挂载点一定是目录，该目录为进入该文件系统的入口。

同一个filesystem的某一个inode只会对应到一个文件内容而已；inode挂载到文件目录上

##### 其他Linux支持的文件系统与VFS

支持：SGI的XFS文件系统、小型文件的Reiserfs、以及Windows的FAT文件系统

传统文件系统：ext2/minix/MS-DOS/FAT/iso9660(光盘)等等；

日志式文件：ext3/ext4/ReiserFS/Windows'NTFS/IBM'sJFS /SGI'sXFS/ZFS

网络文件系统：NFS/SMBFS

Linux VFS(virtual FileSystem Switch)

Linux 的核心是通过virtual filesystem switch的功能区管理和读取文件系统。

![virtual filesystem switch](F:\Linux截图\virtual filesystem switch.PNG)

##### XFS文件系统简介

放弃ext家族的原因：支持度最广，但格式化超慢

ext存在的预先分配inode和block就消耗很大时间

##### XFS文件系统配置

基本是一个日志式文件系统

XFS文件系统在资料的分布上，主要规划三部分：资料区（data section）文件系统活动登录区（log section)、实时运作区（realtime section）

##### 资料区（data section）

包括ext文件系统里面的inode,block,superblock,inode bitmap,block bitmap,inode table,block table等数据，也按block group 存储区群组，特点：inode，block都是系统需要用到时才实时分配的，所以格式化文件系统超级快

另外，与ext家族不同的是，xfs的block与inode有多种不同的容量可供设定，block容量可由512bytes~64K调配，但在Linux的环境下，最高可以使用的block大小为4K,inode 的容量可由256bytes到2M这么大，但256bytes已经够用了

##### 文件系统活动登录区（log section)

登录区这个区域主要用来记录文件系统的变化，有点想日志区，文件的变化会在这里被记录下来，直到giant变化完整的写入到数据区后，该笔记录才会被终结。可以指定外部磁盘来当做XFS文件系统的日志区块，如你可以将SSD磁盘当做XFS的登录区。

##### 实时运作区（realtime section）

当有文件要被建立是，XFS会在这个区段里面找到数个的extent区块，将文件放置在这个区块内，等分配完毕后，再写入到data section的inode与block中，这个extent区块大大小得要在格式化的时候就先指定，最小值是4K最大可到1G，一般有默认最好不要乱动，避免影响到磁盘效能

##### XFS文件系统的描述数据观察

格式：xfs_info 挂载点|装置文件名

#### 文件系统的简单操作

磁盘与目录的容量

df：列出文件系统的整体磁盘使用量；df [-ahikHTm] [目录或文件名]

选项与参数

-a: 列出所有的文件系统

-k：以KBytes的容量显示各文件系统

-m:以MBytes的容量显示各文件系统

-T: 连同该partition的filesystem名称也列出

-i: 不用磁盘容量，而以inode的数量来显示

![df指令](F:\Linux截图\df指令.PNG)

Filesystem:代表该文件系统是在哪个partition

1K-block:说明底下数字单位是1KB

Used:使用掉的磁盘空间

Available:剩下的磁盘空间大小

Use%：就是磁盘的使用率；当使用率达到90%时，需注意

Mounted on :就是磁盘挂载的目录所在（挂载点，文件对应的inode挂载到对应的文件目录上）

df指令读取的数据几乎是整个文件系统，读取的范围主要是superblock内的信息。

##### 这样读取出来的数据都是由根目录衍生出来的，所以当剩余容量剩余0时，这样问题很大。

注意某个分区槽填满



du:  评估文件系统的磁盘使用量（常用在推估目录所占容量）

/dev/shm/目录，实质是利用内存虚拟出来的磁盘空间，通常是物理内存的一半！

由于是内存仿真出来的磁盘，所以有访问速度快，下次开始没有存储。

格式：du [-ahskm]  文件或目录名称

选项与参数

-a: 列出所有的文件与目录容量

-h:以人们比较易读的容量格式（G/M）显示

-s:列出总量而已，

-k     -m

##### 实体链接与符号链接：ln

两种方式：

类似Windows的快捷功能的文件，快速链接到目标文件（或目录）；

##### 透过文件系统的inode连结来产生新文件名，而不是产生新文件（hard link)



##### hard link(实体链接，硬式链接)

> 每个文件都会占用一个inode ,文件内容由inode来记录指向（block)
>
> 想要读取该文件，必须要经过目录记录的文件名来指向正确的inode号码才能读取。

也就是说文件名只与目录有关，但文件内容则与inode有关。hard link的做发就是多个文件名指向同一个inode号码。hard link只是在某个目录下面多增一笔文件名链接到某inode编码的关联记录而已。

![hardLink](F:\Linux截图\hardLink.PNG)

意思：可透过1或2目录的block的记录找到目标文件的inode从而在其中得到记录的目标文件的block编码从而读取目标文件数据。最大的好好处时安全：删除其中一个目录下的文件并不影响实际存储的文件信息及数据。

局限性：hard link 仅能在单一文件系统中进行，不能跨文件系统

不能跨file system

不能link 目录

symbolic link (符号链接，快捷方式)

> Symbolic link 就是在建立一个独立的文件，而这文件会让数据的读取指向它link的那个文件的文件名

当源文件被删除之后，symbolic link 的文件会发生【打不开】，既然无法启动某文件

![symbolic link](F:\Linux截图\symbolic link.PNG)

注：symbolic link 所建立的文件为一个独立的新文件，只是block里面存储的信息时其链接文件的inode信息，所以会占用掉inode与block。

##### 链接操作指令ln

格式：ln [-sf] 来源文件 目标文件

选项与参数

-s: 如果不加任何参数进行链接，那就是hard link ；-s 就是symbolic link

-f: 如果目标文件存在，就主动的将目标文件直接移除后再建立；

关于目录的link数量：新的目录的link数为2，而上一层目录的link数量则会增加1；

#### 磁盘的分区、格式化、检验与挂载

在系统里面新增一颗磁盘：

> 1、对磁盘进行分区，以建立可用的partition;
>
> 2、对该partition进行格式化（format)，以建立系统可用的filesystem；
>
> 3、若想要仔细一点，则可对刚刚建立好的filesystem进行检查；
>
> 4、在Linux系统上，需要建立挂载点（既目录），并将它挂载上去；

磁盘分区格式：MBR、GPT                    分区工具：fdisk、gdisk

查看磁盘分区格式指令：lsblk  [-dfimpt]  [device]                                       lsblk(list block device)

选项与参数：

-d: 仅列出磁盘本身，并不会列出该磁盘的分区数据

-f: 同时列出该磁盘内的文件系统名称

-t:列出该磁盘装置的详细数据，包括磁盘队列机制，预读写的数据量大小等

![lsblk磁盘分区](F:\Linux截图\lsblk磁盘分区.PNG)

name:装置的文件名，省/dev等前导目录

MAJ：MIN :主要：次要装置代码（核心认识的装置都是通过这两个代码来熟悉）

RM:removable device

RO :是否为只读装置

TYPE:是磁盘（disk)、分区槽（partition）还是只读存储器（rom)等输出

MOUTPOINT:挂载点

##### blkid 列出装置的UUID等参数

ＵＵＩＤ(universally unique identifier)全局单一标识符（Linux会将系统内所有的装置都给予一个独一无二的标识符）

指令格式：blkid

装置名称    UUID名称  文件系统类型（TYPE)

parted列出磁盘分区表类型与分区信息

指令格式：parted device_name print

parted  /dev/vda print

分区数据

##### 磁盘分区：gdisk/fdisk（分区表的格式：MBR/GPT）

> MBR分区表使用fdisk分区；GPT分区使用gdisk分区

gdisk

d   delete a partition

n  add a new partiton

p  print the partition table

q quit without saving changes

w write table to disk and exit

流程：Isblk或blkid 先找到磁盘 ------>parted  /dev/xxx  print 找出内部分区表类型------->使用gdisk或fdisk来操作系统

（只要离开gdisk时按下【q】,那么所有的动作【都不会生效】，相反，按下【w】就是所有洞动作生效的意思

![GPT](F:\Linux截图\GPT.PNG)

p:磁盘文件名/扇区数与总容量           单一扇区大小为512Bytes         （可列出目前这可磁盘的分区表信息）

磁盘的GPT标识

这个磁盘总共1GB的容量，共有2097152个扇区，每个扇区容量为512bytes.(现在分区主要是以扇区为最小单位)

gdisk权限root,使用的装置名不要加数字，因为partiton是针对【整个磁盘装置】，而不是某个partition

##### 用gdisk新增分区槽

做一下的分区需求：

1GB的xfs文件系统（Linux）

1GB的vfat文件系统（Windows）

0.5GB的swap（Linux swap![使用gdisk新增分区槽](F:\Linux截图\使用gdisk新增分区槽.PNG)

重点：【Last sector】绝不要使用默认值，因为默认值会将所有的磁盘容量用光；

最好使用【w】保持，并使用【p】查看一下新增槽的划分情况。

![分区槽信息](F:\Linux截图\分区槽信息.PNG)

hex code or GUID(L to show codes, Enter=8300):      Linux选择8200/8300/8e00三种格式，Windows选择0700格式

可使用【L】来进行显示查询

更新Linux核心的分区表信息

指令格式：partprobe [-s]

用gdisk删除一个分区槽【d】

注意：千万不要去处理一个正在使用中的分区槽

#### fdisk

虽然MBR分区表未来应该会被慢慢淘汰，但旧的系统及虚拟机使用上面，还是有小磁盘空间，这时处理MBR分区表，就得使用fdisk

fdisk有时会使用磁柱（cylinder)作位分区的最小单位，与gdisk默认使用的sector不太一样，另外，MBR分区是有限制的（primary,extended,logical等)，其他方面使用基本相同。

#### 磁盘格式化（建立文件系统）

分区完毕之后，进行文件系统的格式化，格式化指令[make filesystem,mkfs]

##### xfs文件系统mkfs.xfs

[格式化]应该称为[建立文件系统（make filesystem]

建立的是xfs文件系统，所以：mkfs.xfs

指令格式：mksf.xfs  [-b  bsize]  [-i parms]  [-L label] [-f] \[-r parms]  装置名称

![mkfs.xfs建立文件系统（格式化）](F:\Linux截图\mkfs.xfs建立文件系统（格式化）.PNG)

重要的是inode与block的数值

![操作系统文件系统查看](F:\Linux截图\操作系统文件系统查看.PNG)

建立文件系统【格式化】可以使用agcount来搭配CPU核心数

##### XFS文件系统for RAID效能优化（Optional）

磁盘阵列（多颗磁盘组成一颗大磁盘）RAID

基本上，磁盘阵列（RAID)就是透过文件先细分为数个小型分区区块（stripe)之后，然后将众多的stripes分别放到磁盘阵列里面的所有磁盘，所有一个文件时被同事写入到多个磁盘去，当然效能会好一些，为了文件的保全性，所以在这些磁盘里面，会保留数个（与磁盘阵列的规划有关）备份磁盘，以及可能会保留一个以上的备用磁盘（spare disk) ，这些区块基本上会占用磁盘阵列的总容量，不过这样数据比较有保障。

分区区块stripe大小4k~1M之间；与支持的文件大小有关case by case

文件系统的读写要能够有优化，最好搭配磁盘阵列参数来设计。可在文件系统里面讲stripe规划好，交给ARID去存取。

数据宽度（swidth)=stripe大小*磁盘数量

![磁盘阵列RAID参数设置](F:\Linux截图\磁盘阵列RAID参数设置.PNG)

##### EXT4文件系统mkfs.ext4

如果想格式化为ext的传统Linux文件系统，可使用mkfs.ext4指令

指令格式：mkfs.ext4  [-b size]  [-L label]  装置名称

-b : 设定block大小，有1K,2K，4K的容量

-L ： 后面接这个装置的标头名称

![建立文件系统（格式化）为ext4文件系统](F:\Linux截图\建立文件系统（格式化）为ext4文件系统.PNG)

##### 其他文件系统mkfs

可使用mkfs [tab] 查看本操作系统支持的文件系统，即可格式化（建立文件系统）种类。

#### 文件系统检验

原因：文件系统运行时会有磁盘与内存数据异步状况发生，若莫名当机非常有肯能导致文件系统错乱

主要针对：xfs和ext4文件系统挽救操作。

##### xfs_repair处理XFS文件系统

指令格式：xfs_ripair [-fnd] 装置名称

选项与参数：

-f: 后面的装置其实是个文件而不是实体装置

-n: 单纯检查并不修改文件系统的任何数据

-d: 通常用在单人维护模式底下，针对根目录（/)进行检查与修复动作，很危险，不要随便用。

```
xfs_repair /dev/sda4      #检查刚刚建立的xfs文件系统
```

修复时该文件系统不能被挂载



##### fsck.ext4处理EXT4文件系统

对于ext4文件系统修复使用fsck.ext4修复指定

指令格式：fsck.ext4  [-pf] [-b superblock]  装置名称

注意：通常只有身为root且文件系统有问题的时候才使用这个指令，否则在正常情况下使用修复指令，可能会造成对系统的危害。

执行 xfs_repair /fsck.ext4时，被检查的partition务必不可挂载到系统上，才能进行有关的修复操作。

#### 文件系统挂载与卸除

挂载点：目录

目录：是进入磁盘分区槽（文件系统）的入口

挂载前:

> 单一文件系统不应该被重复挂载在不同的挂载点（目录）中；
>
> 单一目录不应该重复挂载多个文件系统
>
> 要作为挂载点的目录，理论上都是空目录才行

如果挂载目录不是空，那么挂载文件系统后，原目录下面的东西就会暂时的消失。

要将文件系统挂载到Linux系统上，需使用mount指令

指令格式：

mount -a           

mount  [-l]

mount  [-t  文件系统]  LABEL=''  挂载点

mount [-t  文件系统]  UUID=''   挂载点      #推荐

mount  [-t  文件系统]  装置文件名  挂载点

选项与参数：

-a： 依照配置文件/etc/fstab的数据将所有未挂载的磁盘都挂载上来

-l : 单纯的输入mount会显示目前挂载的信息，加上-l可增列Label名称

-t : 可以加上文件系统种类来指定欲挂载的信息。常见的Linux支持类型有xfs，ext3,ext4,reiserfs ,vfat,iso9660(光盘格式),nfs,cifs,smbfs(后三种为网络文学系统类型)

/etc/filesystem:系统指定的测试挂载文件系统类型的优先级

/proc/filesystems:Linux系统已经加载的文件系统类型

/lib/modules/$(uname -r)/kernel/fs/

##### 挂载xfs/ext4/vfat等文件系统

> 1.找出/dev/sda4的UUID
>
> 2.mount UUID="UUID"   挂载目录

![文件系统挂载到目录下](F:\Linux截图\文件系统挂载到目录下.PNG)

##### 挂载CD或DVD光盘

![挂载光驱](F:\Linux截图\挂载光驱.PNG)

光驱一挂载之后就无法退出光盘，除非将其卸除挂载才能够退出。

##### 挂载vfat（USB)

/dev/sda1 :UUID=“.............”   type="vfat"

对于没有操作系统中没有支持的文件系统格式，需要安装对应的文件系统格式才能处理

yum服务器网络安装

##### 重新挂载根目录与挂载不特定目录

目录树中最重要节点为根目录（/)

重新挂载方式两种：

> 1.reboot
>
> 2.mount -o remount,rw,auto /

根目录通常会被系统挂载为只读，所以【- o remount,xx】的选项也参数很重要

可利用mount来将某个目录挂载到另一个目录去，这并不是挂载文件系统，也可使用symbolic link来连接，

当有些不支持symbolic link 连接时，可使用这样实现连接。

![某个目录挂载到另一个目录中目录的block的内容显示为同一个inode](F:\Linux截图\某个目录挂载到另一个目录中目录的block的内容显示为同一个inode.PNG)

透过mount --bind  目标目录  源目录  实现挂载操作

##### umount(将装置文件卸除)

指令格式：umount  [-fn]  装置文件名或挂载点

选项与参数

-f:  强行卸除！可用于类似网络文件系统（NFS)无法读取的情况；

-l:  立刻卸除文件系统，比-f 还强；

-n:  不更新/etc/mtab情况下卸除

注：若正在使用该文件系统，那么无法卸除该挂载；解决方法：离开该文件系统的挂载点，例如使用[cd /]返回根目录再进行卸除。

#### 磁盘/文件 系统参数修订

动因：希望修改目前文件系统的一些相关信息，例如：修改Label name，或journal参数。

mknod

Linux的装置以文件来代表，那么文件如何代表该装置（透过文件的major与minor数值来代替，既major与minor数值是有特殊意义）

![目录dev-sda装置信息](F:\Linux截图\目录dev-sda装置信息.PNG)

8代表主要装置代码（Major)，0~5则为次要装置代码（Minor),Linux核心认识的装置数据就是透过这两个值来决定的

Linux 核心2.6版本以后，硬件文件名已经可以被系统自动实时产生；但某些情况需要手动处理装置文件（例如：在某服务别关到特定目录下时chroot)

指令格式：mknod  装置文件名  [bcp]  [Major]  [Minor]

选项与参数：

b: 设定装置名称成为一个周边存储设备文件，例如磁盘等

c:  设定装置名称成为一个周边输入设备文件，例如鼠标/键盘等

p:  设定装置名称为一个FIFO文件

Major: 主要装置代码

Minor:次要装置代码；

![手动修改装置文件信息](F:\Linux截图\手动修改装置文件信息.PNG)

FIFO:文件类型为P

这不是普通文件，所若不是真正需要，测试完成后删除改装置文件。

##### xfs_admin修改XFS文件系统的UUID与Label name

如果格式化时忘记加上标头名称，后想再次加入时，不需要重复格式化，直接使用xfs_admin指令修改

指令格式：xfs_admin [-lu] [-L label] [-U uuid] 装置文件名

选项与参数：

-l: 列出这个装置的label name

-u：列出这个装置的UUID

-L: 设置这个装置的label name

-U: 设定这个装置的UUID

