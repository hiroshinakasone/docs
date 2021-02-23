# ループデバイスを使う

## 概要
ループデバイスは、Linuxの機能の一つで、ファイルをブロックデバイスのように扱うことができるようにしたもの。

ファイルから作成したループデバイス上にファイルシステムを構築し、これをシステムにマウントすることで、擬似的なディスクとして利用することができる。

ループデバイスの管理は `losetup` コマンドを使う。

## キーワード
ループデバイス, Loop Device, losetup, Linux

## 使用方法

### ループデバイスの一覧を表示する
```sh
$ losetup -l
```

### ループデバイスを作成する

1. 任意のサイズのファイルを作成する

    この例では `dd` コマンドを使って128MiBのファイルを作成する。`dd` コマンドではなくても、任意のサイズのファイルが作成できればなんでもよい。

    ```
    $ dd if=/dev/zero of=./disk01 bs=1024 count=131072
    ```

2. ループデバイスを作成する

    作成したファイルを指定してループデバイスを作成する。作成にはルート権限が必要。

    `losetup -l` でループデバイスが作成されたことを確認する。ここでは `/dev/loop28` として作成されている。

    ```sh
    $ sudo losetup -f ./disk01
    $ losetup -l | grep disk01
    /dev/loop28         0      0         0  0 /path/to/disk01             0     512
    ```

### ループデバイスにファイルシステムを構築する

1. ファイルシステムのフォーマット

    この例ではext4でファイルシステムを構築する。
    ```sh
    $ sudo mkfs.ext4 /dev/loop28
    mke2fs 1.45.5 (07-Jan-2020)
    Discarding device blocks: done
    Creating filesystem with 32768 4k blocks and 32768 inodes

    Allocating group tables: done
    Writing inode tables: done
    Creating journal (4096 blocks): done
    Writing superblocks and filesystem accounting information: done
    ```

2. マウント
    ```sh
    $ sudo mount -t ext4 /dev/loop28 /media/username/mount_point
    ```

3. アンマウント
    ```sh
    $ sudo umount /media/username/mount_point
    ```

### ループデバイスを削除する
```sh
$ sudo losetup -d /dev/loop28
```

## 環境
```sh
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.2 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.2 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal

$ uname -srvmpio
Linux 5.8.0-43-generic #49~20.04.1-Ubuntu SMP Fri Feb 5 09:57:56 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

$ loseup --version
losetup from util-linux 2.34
```

---
(c) 2021 NAKASONE Hiroshi