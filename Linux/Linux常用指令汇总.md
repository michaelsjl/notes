---
tags: [Linux]
title: Linux常用指令汇总
created: '2021-02-01T09:46:19.008Z'
modified: '2021-02-01T09:54:58.532Z'
---

# Linux常用指令汇总

查看防火墙端口
`firewall-cmd --zone=public --list-ports`

防火墙加端口
`firewall-cmd --zone=public --add-port=8080/tcp --permanent`

防火墙减端口
`firewall-cmd --zone=public --remove-port=8080/tcp --permanent`

防火墙重启
`firewall-cmd --reload`



