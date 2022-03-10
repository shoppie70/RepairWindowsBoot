# How to Fix on Windows

About how to repair Windows boot problems due to `SSD clone`, `Fail Windows Update` etc.

This is a common problem when cloning slow 1TB hard drive data to 256GB SSD.  
Boot your PC after cloning to SSD, it's displayed an error message about `Operating System Not Found` or `No bootable device -- insert boot disk and press any key`.  

.....or it just shows `_` on a blank screen.  

Next, You have to make recovery drive like [this introduction](https://support.microsoft.com/en-us/windows/create-a-recovery-drive-abb4691b-5324-6d4a-8766-73fab304c246#WindowsVersion=Windows_10) and open `cmd.exe`.

## Boot Area Repair

### 1. Scan bootable operating system

```shell
$ bootrec /scanos
```

### 2. Rebuild BCD

```shell
$ bootrec /RebuildBcd
```

### 3. Fix Master Boot Record(MBR)

```shell
$ bootrec /fixmbr
```
### 4. Boot area recovery

```shell
$ bootrec /fixboot
```

>`bootrec /fixboot` is a command that can fix the problem that Windows does not start due to the corruption of the boot area.

## `bootrec /fixboot` says" Access is denied "

In Japanese, says `アクセスが拒否されました`.

### Check Disk in Boot area

#### 1. Find the unlabeled drive.

```shell
$ diskpart

diskpart> list volume
```

#### 2. Select Volume of unlabeled drive.

```shell
diskpart> select volume X
```
> X is 1,2,3......

#### 3. Label the Drive.

```shell
diskpart> assign letter b:
diskpart> exit
```

#### 4. Run `chkdsk`.

```shell
$ chkdsk b: /r
```

### Rebuild BCD Forcibly

#### Format EFI partition (option)

```shell
$ format b: /FS:FAT32
```

<!-- BCDの修復
```shell
$ bcdedit /export c:\bcdbackup(元のBCDストアをバックアップする)
$ attrib c:\boot\bcd -h -r -s(BCDファイルの隠し属性、読み取り専用属性、およびシステム属性を解除する)
$ ren c:\boot\bcd bcd.old(BCDストアの名前を変更する)
$ bootrec /rebuildbcd
``` -->

#### Rebuild BCD command
```shell
$ cd /d b:
$ bootrec /Rebuildbcd
$ bootrec /fixboot
$ bcdboot c:\Windows /l ja-JP /s b: /f ALL
$ exit
```

> Paths like `c:\Windows` vary depending on your environment.

### Tricks 🎉

`chkdsk`  can run GUI.

```shell
$ notepad
```

1. Open `notepad` to use above command.
2. Click `File`
3. Click `Open`
4. It appers `Explorer Window`, then Right click `System Drive`
5. Click `Property`, then you can execute `Check Disk`

