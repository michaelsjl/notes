---
tags: [QT]
title: mac 签名公证步骤
created: '2021-02-07T08:07:13.553Z'
modified: '2021-02-07T08:07:27.458Z'
---

# QT开发程序 app 签名全过程

## 情况简介
mac 10.15 版本以上，apple 对于安全性能要求更高了。如果我们开发的app，打包成dmg后，上传在服务器后台，前端http下载下来以后，进行安装，系统会检测到不合法，不允许打开该app。    
后来进过查阅很多资料，终于发现，需要对我们自己的app先签名，再上传上去进行公证，公证成功了以后，就可以用了。  
相当于xcode上的Build后，再Archive，再上传Distribute App，但Xcode上一直不能成功，主要是对于QT相关的framework一直不能签名成功。  
后来发现可以直接进行命令行去操作，那就可以绕开xcode了，读者有兴趣可以后期自行研究如何xcode上进行。

## 操作原理
1. 首先要先在电脑上安装证书（具体怎么安装有点忘了，后面想到了，我再补充。。）
2. 设置app一个专用的密码  
[使用App专用密码](https://support.apple.com/zh-cn/HT204397)  
设置好的时候记住那个密码，再公证的时候要用到  
3. 对app 签名
4. 将app 打包成dmg，对dmg再签名
5. dmg 上传进行公证，公证成功，代表apple对你的dmg已经通过了审核。
6. 主要签名最主要的是要对app里面的所有第三方的都要签名，包括qt、java等等，漏掉任何一个，公证都不会成功的。

## 具体步骤
1. 确保java包是在版本10.9 SDK以上

2. qt上正常的编译一个app

3. 编译出的app bundle里面替换签名的fdt-qt.jar
1) 新建一个文件夹，将app里面的fdt-qt.jar拷贝进去
2）解压：`jar -xvf fdt-qt.jar`
3) 删除原fdt-qt.jar包
4）签名：
`/usr/bin/codesign --force --sign "Developer ID Application: ZheJiang JiaWo Network Technology Co., Ltd (S63GSCD749)" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ./org/lirc/socket/*`
5) 压缩：`jar -cvfM0 fdt-qt.jar ./`
6) 将签完名的fdt-qt.jar包将app里面的jar包替换掉

4. 对app里面的所有文件进行签名，自己定义一个sign.sh的脚本：脚本内容见附件。

5. 对脚本“sign.sh”加上执行权限 `chomd 755 ./sign.sh`

6. 对app文件设置执行权限 `chmod -R 755 transfer.app`

7. 执行脚本：`./sign.sh`

8. 检查签名情况 `codesign -v --strict --deep --verbose=2 transfer.app`
如果是返回：
```
transfer.app: valid on disk
transfer.app: satisfies its Designated Requirement
```
代表app签名成功

9. 检查app签名有关的文件 `codesign -d --deep --verbose=2 -r- transfer.app`

10. 将app 打包进dmg里面（详细教程在git QContentHub/transfer仓库, doc/handover/install.md）

11. 对dmg签名  
`/usr/bin/codesign --force --sign "Developer ID Application: ZheJiang JiaWo Network Technology Co., Ltd (S63GSCD749)" --deep -o runtime --preserve-metadata=identifier,entitlements,flags transfer_mac_0.0.3.48.dmg`

12. 上传进行公证，一般等待3~5分钟  
`xcrun altool --notarize-app --primary-bundle-id "com.southbaytech.transfer.TransferApp" --username "apple@southbaytech.co" --password "xzsm-itlc-agwm-bnia" --asc-provider "S63GSCD749" -t osx --file transfer_mac_0.0.3.48.dmg`

13. 上传公证成功后会得到一个ID，例如：  
`RequestUUID = a693d492-e96a-4720-b101-7a987bc989a7`

14. 根据ID检查公证结果  
`xcrun altool --notarization-info a693d492-e96a-4720-b101-7a987bc989a7 --username "apple@southbaytech.co" --password "xzsm-itlc-agwm-bnia"`

15. 如果返回：
```
No errors getting notarization info.

    Date: 2021-02-07 02:03:57 +0000
    Hash: f208c2cbd69b067c4417f6d898f5b3794b9e909f3d200401914c9e5d984eb839
    LogFileURL: https://osxapps-ssl.itunes.apple.com/itunes-assets/Enigma114/v4/6b/ee/82/6bee82ce-ddbe-dd2c-bc52-95659eaaf0aa/developer_log.json?accessKey=1612858097_3032910115143252691_%2BJ5BAEIb5kYlx2wHf4L2LT6%2ByEuDlzuv3TgMhvasa8ubdTdMTNh%2Bzu%2BgqFKHSBGrx5ablZKv%2BAfRD26GCSYRZ%2B07otTHaMNhEARSgop%2B%2FskCC5RltVlXFwAYF4Ao%2BwXHeIuFCYFywn1CzO%2BQMXLMgfpylKtsbpo7eXjox3mziOc%3D
    RequestUUID: a693d492-e96a-4720-b101-7a987bc989a7
    Status: success
    Status Code: 0
    Status Message: Package Approved
```
代表公证成功了，这个时候，可以将dmg就可以用了。

## sign脚本
```
#! /bin/bash

apppath="/Users/lt011/Michael/test/transfer.app"

frameworkpath="$apppath/Contents/Frameworks/"
pluginpath="$apppath/Contents/PlugIns/"

jkspath="$apppath/Contents/MacOS/JKS/"
toolpath="$apppath/Contents/MacOS/tool/"
autogradepath="$apppath/Contents/MacOS/autoUpgrade"
fdtqtjarpath="$apppath/Contents/MacOS/fdt-qt.jar"
quazippath="$apppath/Contents/MacOS/libquazip.1.dylib"
tuspath="$apppath/Contents/MacOS/libTUS.1.dylib"

cert="Developer ID Application: ZheJiang JiaWo Network Technology Co., Ltd (S63GSCD749)"
certInstall="3rd Party Mac Developer Installer: ZheJiang JiaWo Network Technology Co., Ltd (S63GSCD749)"
entitlementPath="/Users/lt011/Michael/test/transfer.entitlements"

# codesign
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtCharts.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtConcurrent.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtCore.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtDBus.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtGui.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtNetwork.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtPrintSupport.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtSql.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtSvg.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtWebSockets.framework
/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${frameworkpath}QtWidgets.framework

/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${fdtqtjarpath}

/usr/bin/codesign --force --sign "${cert}" --deep -o runtime --preserve-metadata=identifier,entitlements,flags ${apppath}
```

## 参考网址




