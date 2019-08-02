---
title: Ubuntu Notes
date: 2019-08-02 22:41:12
tags:
- linux
- note
categories:
- os
---

# Ubuntu Notes

[TOC]

----

### Command
#### 查看软链接实际指向的文件位置
```bash
$ ls -l <filename>
```

#### ssh-keygen
1. 在master生成秘钥, `id_rsa, id_rsa.pub`或`id_dsa, id_dsa.pub`：
```bash
$ ssh-keygen -t [rsa|dsa]

$ ls ~/.ssh
id_rsa  id_rsa.pub  known_hosts

$ cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
......
......
......
iXQVQHy5W5hX8kykh9PfVeh745kzWDQqcp9JOBlaWcRxY+g7A5xfYaEeTQqjd/ZX8692UP
I/EFmILN9N26WIE3AAAAD2Z3ekBmd3otbWJwLmxhbgECAw==
-----END OPENSSH PRIVATE KEY-----

$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCpQZCHx6E5sj3/AZec3nFBjnWi83g/9brGVuiAV8yv+q/PksiGmZ5XGoGStq6iG9DWVLJa1Tv26Kh8Evi9rvpSbGaPbPPA09Gzi+UNZMieydSeqpFIDanB0X9H8VfGEcOzIw21wOVtIYthu5cwyjdfO3zYAV/scJcQon4m/bttk3cAPmnaQXw2QVg/gFWVdSadgRC1MaQJcG5zJxEdVKSHYyk6gCVKZLYyc029iZndbNfqtKcsV+vkuSCwY/nD/5B+GqnXjPSgVL9I7QIC8Gf9k+C012hBF3Vir5p1edft2Fn+CN77WcOyj9ye/ZSLvNDgW5W/0Ab27klWe/pI1eBT fwz@fwz-mbp.lan
```
2. 把master生成的公钥发送到slave
```
$ ssh-copy-id -i ~/.ssh/id_rsa.pub username@ip_address
```
此时，在slave上生成authorized_keys文件，内容为id_rsa.pub中的内容。

3. 从master通过ssh登录到slave，免密成功。

