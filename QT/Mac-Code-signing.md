---
tags: [QT]
title: Mac秘钥资料
created: '2020-12-24T03:47:36.510Z'
modified: '2020-12-24T03:47:54.701Z'
---
# Mac 秘钥 资料

## 私钥 Private Key:
Keychain的app来管理    

## UDID
UDID （Unique Device Identifier）是区分物理设备的唯一标识。    
所有你的iPhone肯定有一个与众不同的UDID，包括你的iPad等等。    
通常，UDID会是一个40位十六进制字符串。    

## Certificate 认证
个人开发者认证 Apple Worldwide Developer Relations Certification Authority Certificate    
获取一个认证，你需要子啊Keychain Access中生成一个 Certificate Signing Request，并把它发给Apple。这会创建一对钥-私钥（如果还没有的话）。    
Apple收到后会验证信息，然后给你创建一个认证。    

## App ID
在Xcode中创建一个项目，你要提供 Product Name 和 Company Identifier的信息。   
Product Name，就是你的app的一个较短的名称。    
Company identifier，通常会是一个反转的DNS串，例如：com.mycompanyname    
这两者合起来，就似乎Bundle Identifier。你会在Info.plist中找到。    

## Provisioning Profile
把前面讲到的东西合起来：    
·App ID （app的唯一标识）    
·UDID （在某台设备上运行的唯一标识）    
·Certificate （开发者认证）
就是所谓的Provisioning Profile。    

Provisioning Profile 通过xcode或者itunes导入到device中，或者打包到一个包含app和profile的.ipa文件中。    


