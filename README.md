# FSOD - File System of D state

Simple file system to create uninterruptive sleep process (D state in Linux, U state in MacOS).
This file system is created for the purpose of debugging and situation reproduction .

## How to use

First, get ready for `fusepy`. You may use both python 2/3.

```
pip install fusepy
```

Then, mount this file system to wherever you want.

```
mkdir /tmp/fsod
sudo python fsod.py /tmp/fsod      # mount this file system to /tmp/fsod
```

If you try to `read(2)` whatever file on this file system, the process will be in an unkillable state.
One example will be like this, using another terminal:

```
touch /tmp/fsod/abcde
cat /tmp/fsod/abcde &
kill -9 %1
ps auxww | grep [a]bcde
```

To recover from D state, please `kill -KILL` the `fsod.py` process.

## 解説

なんか `kill -KILL` できないプロセスってできるじゃないですか。 `ps` すると `D+` とかになってるやつ。あれを作るための file system を FUSE で作ったものです。
D state は NFS などのネットワークストレージを使っているとよく見るものですが、要するに `read(2)` 等をしている最中にカーネルモジュール側からの回答が返ってこなくなったときに起きます。
ということは、 fuse で十分再現ができるんじゃないかなーということで作ってみた習作です。

コンテナに D state になってほしい場合、ホスト側でこれを mount しておいて、コンテナに bind mount して中で触るのがよいかなと思います。
