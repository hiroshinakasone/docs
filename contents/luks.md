# LUKSでディスクを暗号化する

## 概要
LUKS (Linux Unified Key Setup)を使ってディスクを暗号化をする。

LUKSはブロックデバイスを暗号化するための仕様で、透過的にデバイスを暗号化することができる。
暗号化に使うキーは、キーファイルまたはパスフレーズを使用する。
また、キーは複数設定することができる。

デバイスのフォーマット、ロック、ロック解除などの操作は `cryptsetup` というユーティリティコマンドを使用する。


## キーワード
LUKS (Linux Unified Key Setup), ディスク暗号化, 透過的データ暗号化, TDE (Transparent Data Encryptio), cryptsetup, Linux

## 手順
1. デバイスをLUKS形式でフォーマットする

    ハードディスクなどのブロックデバイスをLUKS形式でフォーマットする。フォーマット後、デバイスにLUKSパーティションが作成される。パスフレーズの登録が求められるので、入力する。
    ```
    $ sudo cryptsetup luksFormat /dev/sda
    ```

2. 登録したパスフレーズを使ってロックを解除する

    デバイスを指定して、ロックを解除する。この際、device mapper が作成されるので、この名前も指定する必要がある。この例ではわかりやすいように `luks-` の接頭辞を付けている。
    ```
    $ sudo cryptsetup open /dev/sda luks-xxxxx
    $ ls -l /dev/mapper
    lrwxrwxrwx 1 root root       7  2月 27 17:48 luks-xxxxx -> ../dm-0
    ```

    ロック解除後は `/dev/mapper/luks-xxxxx` をブロックデバイスとして利用することができる。以下はext4でファイルシステムを構築する例。

    ```
    $ sudo mkfs -f ext4 /dev/mapper/luks-xxxxx
    ```

3. デバイスをロックする

    ロック解除時に作成された device mapper を指定してロックをかける。
    ```
    $ sudo cryptsetup close /dev/mapper/luks-xxxxx
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

## 参考
- [Ubuntu Manpage: cryptsetup - manage plain dm-crypt and LUKS encrypted volumes](http://manpages.ubuntu.com/manpages/focal/en/man8/cryptsetup.8.html)
- [第8章 LUKS を使用したブロックデバイスの暗号化 Red Hat Enterprise Linux 8 | Red Hat Customer Portal](https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/8/html/security_hardening/encrypting-block-devices-using-luks_security-hardening)

---
(c) 2021 NAKASONE Hiroshi
