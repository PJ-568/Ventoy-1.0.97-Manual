# Ventoy-1.0.97-手工部署教程

> 作者：[@ksjifjui](http://bbs.wuyou.net/home.php?mod=space&uid=869921)
>
> 来源：`https://note.youdao.com/s/UrducsjZ`
>
> 起因：[Ventoy也可与你的操作系统和谐共存于同一硬盘，体验不一样的玩法 - Ventoy - 无忧启动论坛 - Powered by Discuz!](http://bbs.wuyou.net/forum.php?mod=viewthread&tid=432540&extra=&page=1)

**直接点击下面的网盘的下载链接，可能会提示页面不存在，手动复制链接并粘贴到浏览器地址栏后，敲回车即可正常访问。**

[https://pan.baidu.com/s/1-eHEZMLzqDoE0Up1SY4Fsw?pwd=u53z](https://pan.baidu.com/s/1-eHEZMLzqDoE0Up1SY4Fsw?pwd=u53z) (后期版本不再更新)

## 1.0.97版本已更新完毕

后期不再安排更新到其他版本。

如急需最新版本也可以在无忧论坛中留言，后期时间允许的话，也会考虑重新编译最新版本。

## U盘或硬盘支持手工部署UEFI版的前提条件

1. U盘或硬盘现有分区结构中
   - 第1分区为Ventoy启动分区（存放Ventoy启动文件），格式必须是FAT格式，分区起始扇区号依据自身需求确定（如果是UEFI启动则根据自身需求确定起始扇区号；如果是BIOS启动，则建议从2048号扇区开始，因为要在MBR中写入对应的启动文件，需要一定的保留空间），分区大小依据自己需求确定。
   - 第2分区为Ventoy的ISO分区及插件分区，可以是NTFS, EXT等格式。可参照下图分区结构：Windows系统采用UEFI方式启动，默认为下图分区结构，如果有MSR和ESP两个分区，则可将MSR分区合并到ESP，将压缩包中Ventoy相关的启动文件全部拷贝到现有EFI分区，不用再新建分区，也不用格式化U盘或硬盘，不改变现有硬盘分区结构

   ![分区结构示例](./_resources/fc30d3e669337f866b12d41100cd7253)

2. BIOS设置为UEFI启动，关闭安全启动，压缩包中只提供了x64的UEFI方式启动文件，如需要其他平台的UEFI启动文件，可以给我发消息

## 手动部署UEFI版(x64)到电脑硬盘，与Windows、Linux系统共存步骤

1. 解压并拷贝所有内容到硬盘第1个FAT分区的根目录
2. 将Ventoy启动文件路径：EFI\VENTOY\grubx64_real.efi 添加到UEFI启动引导器对应的配置文件中，如：rEFind的refind.conf，Grub2的grub.cfg等
3. 硬盘第2分区（一般是windows或linux系统所在分区）现在变成了Ventoy的ISO分区和插件分区，将ISO文件及Ventoy插件等，保存在这个分区下；强烈建议在该分区中添加Ventoy配置文件，否则每次打开Ventoy的时候，会出现"Ventoy scanning files, please wait..."的提示，这是因为第2分区中文件太多造成的，Ventoy的自动搜索功能要遍历该分区下所有的文件夹，系统文件夹数量众多，一时半会肯定无法全部扫描完成。所以强烈建议在该分区下添加一个搜索根目录配置，具体配置项，参照官方链接：[https://www.ventoy.net/cn/doc_search_path.html](https://www.ventoy.net/cn/doc_search_path.html)

### UEFI启动引导器添加Ventoy启动引导

1. **rEFind**

    ```plaintext
    loader /EFI/VENTOY/grubx64_real.efi
    ```

2. **GRUB 2**

    ```plaintext
    chainloader  /EFI/VENTOY/grubx64_real.efi
    ```

3. **Systemd-boot**

    ```plaintext
    efi  /EFI/VENTOY/grubx64_real.efi
    ```

#### UEFI启动引导器成功启动后的效果，以rEFInd演示

![UEFI启动引导器](./_resources/5140d5402cc976b8e226835c4f8ce099)

### 手动部署BIOS版到电脑硬盘，与Windows、Linux系统共存步骤

1. 解压并拷贝所有内容到硬盘第1个FAT分区的根目录
2. 添加根目录下的vtldr文件到现有的mbr引导器，如bootmgr、xorboot、grub2、grub4dos等

### MBR启动引导器添加Ventoy启动引导

1. 通过bootmgr引导，添加到当前windows系统的bcd文件中

    ![添加引导](./_resources/e652613df3a5b77a3a375295d440a08a)

    bootmgr启动效果演示图

    ![bootmgr启动效果](./_resources/dbe56c38bca42a3feef973c9733e097b)

2. 通过xorboot引导，配置xorboot启动菜单

    ![配置xorboot启动菜单](./_resources/591e4df0e634aa50eb79539caf54f651)

    xorboot启动效果演示图

    ![xorboot启动效果](./_resources/711756e7734f302182bfca55103e9766)

3. 通过grub2引导，grub.cfg中添加如下代码

    ```plaintext
    menuentry 'Ventoy' {
        insmod ntldr
        search --file /vtldr --set=root
        ntldr /vtldr
    }
    ```

    grub2启动效果演示图

    ![grub2启动效果](./_resources/7ac69f8ac03c803a717fb98ef13134ba)

4. 通过grub4dos引导，menu.lst添加如下代码

    ```plaintext
    title Ventoy
    find --set-root /vtldr
    kernel /grub/i386-pc/core.img
    boot
    ```

    grub4dos启动效果演示图

    ![grub4dos启动效果](./_resources/6080bffd42862d85be8a2a2cf0602516)
