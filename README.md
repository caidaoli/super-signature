# README.md

![微信公众号.png](https://gitee.com/togettoyou/picture/raw/master/2022-2-9/1644374999459-weixin.jpg)

# JetBrains 开源证书支持

感谢 JetBrains 提供的免费授权

<a href="https://www.jetbrains.com/?from=togettoyou" target="_blank"><img src="https://user-images.githubusercontent.com/55381228/127271051-14879011-41dd-4d1b-88a2-1591925b51de.png" width="250" align="middle"/></a>

## 用途

仅提供给个人开发者测试分发自己的应用，苹果个人开发者账号名下有 100 台测试设备可以绑定进行测试分发 APP，本应用仅允许开发者用于分享自己的应用，不可用作其它用途。

## 声明

本应用功能仅是调用 [zsign](https://github.com/zhlynn/zsign) 和 [App Store Connect API](https://developer.apple.com/documentation/appstoreconnectapi) 实现，如有侵权，请与我联系，会及时处理。

严禁使用本软件进行盈利、损坏官方、散落任何违法信息等行为。

本人不再对此软件进行维护。

## 这是什么

一个用 go 实现的 iOS 签名分发应用

使用本应用可以进行 IPA 重签名分发

实现功能：苹果开发者账号管理、IPA安装包管理

运行环境：Docker 或 centos 7

核心签名使用 [zsign](https://github.com/zhlynn/zsign) ，感谢

## Docker 运行

```shell
# 查看帮助
docker run --rm togettoyou/super-signature:latest -h
# 版本
docker run --rm togettoyou/super-signature:latest -v
```

```shell
# http 方式部署，ssl 证书部署可以自行使用 nginx 等网关，或支持 https 的内网穿透等方式
mkdir super-signature
cd super-signature
docker run --name super-signature \
  -v $PWD/ios:/root/super-signature/ios \
  -v $PWD/db:/root/super-signature/db \
  -p 8888:8888 \
  togettoyou/super-signature:latest \
  --url=https://你的域名
# 运行后会挂载容器内 ios目录(存放账号和ipa文件) 和 db目录(存放sqlite文件) 到当前目录下
```

```shell
# https 方式部署
mkdir super-signature
cd super-signature
mkdir ssl
# 自行向服务厂商申请域名的 ssl 证书后拷贝 server.crt 和 server.key 到 ssl 目录
docker run --name super-signature \
  -v $PWD/ios:/root/super-signature/ios \
  -v $PWD/db:/root/super-signature/db \
  -v $PWD/ssl:/root/super-signature/ssl \
  -p 443:443 \
  togettoyou/super-signature:latest \
  --url=https://你的域名 \
  --port=443 \
  --crt=ssl/server.crt \
  --key=ssl/server.key
```

## 可支持阿里云 OSS 作为 IPA 下载服务

```shell
# 使用 enableOSS 开启 oss 支持
docker run --name super-signature \
  -v $PWD/ios:/root/super-signature/ios \
  -v $PWD/db:/root/super-signature/db \
  -v $PWD/ssl:/root/super-signature/ssl \
  -p 443:443 \
  togettoyou/super-signature:latest \
  --url=https://你的域名 \
  --port=443 \
  --crt=ssl/server.crt \
  --key=ssl/server.key \
  --enableOSS=true \
  --ossEndpoint=oss-cn-xxx.aliyuncs.com \
  --ossAccessKeyId=XXXXXXXXXXXXXXXXX \
  --ossAccessKeySecret=XXXXXXXXXXXXXXXXXXXX
```

## 使用 centos 7 自行编译运行

```shell
git clone https://github.com/togettoyou/super-signature.git
cd super-signature
# go 1.16+
make
yum install -y openssl openssl-devel unzip zip
cp zsign/zsign /usr/local/bin/
chmod +x /usr/local/bin/zsign
./super-signature-app -h
# http
./super-signature-app --url=https://你的域名
# https
./super-signature-app --url=https://你的域名 --port=443 --crt=ssl/server.crt --key=ssl/server.key
```

## 查看效果

访问你的域名 https://你的域名/swagger/index.html

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1077776/1622719814015-5552a7a4-496a-4271-b43f-7f78592176d1.png#clientId=uc4af6cdf-c3d2-4&from=paste&height=827&id=u84b71819&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1654&originWidth=2880&originalType=binary&size=275056&status=done&style=none&taskId=ua10a445e-d046-46a0-b6ef-617fde81539&width=1440#id=PdB8i&originHeight=1654&originWidth=2880&originalType=binary&ratio=1&status=done&style=none)


## 使用说明

1、 上传苹果开发者账号信息

登陆 [https://appstoreconnect.apple.com/access/api](https://appstoreconnect.apple.com/access/api) 获取p8(下载的API密钥文件内容)，kid (
密钥ID)，Iss (Issuer ID)：

![](https://cdn.nlark.com/yuque/0/2021/png/1077776/1614157937920-e048fc1b-b8ef-4b08-a559-bcf0a9b72c39.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_14%2Ctext_Z2l0aHViL3RvZ2V0dG95b3U%3D%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10#from=url&id=ipJUH&margin=%5Bobject%20Object%5D&originHeight=970&originWidth=3284&originalType=binary&ratio=2&status=done&style=none)

上传：

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1077776/1623042480919-37ecee18-c7e7-4e17-91ac-c2ad8b7e117a.png#clientId=uab37fe2a-4554-4&from=paste&height=821&id=u8d372f30&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1641&originWidth=2880&originalType=binary&ratio=2&size=239573&status=done&style=none&taskId=ueb474557-a63b-43a0-97c2-b066502a2a4&width=1440)

2、 上传IPA

![image.png](https://cdn.nlark.com/yuque/0/2021/png/1077776/1623042643053-67a10d99-3359-4ebb-9ee4-b36d7ea48bdb.png#clientId=uab37fe2a-4554-4&from=paste&height=822&id=udac83704&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1644&originWidth=2880&originalType=binary&ratio=2&size=240127&status=done&style=none&taskId=ub147db0c-bab9-4419-abef-6de3e71fb46&width=1440)

3、 iPhone 使用 Safari 浏览器打开 AppLink 链接

`/api/v1/getAllPackage` 返回数据格式说明

```json
{
  "code": 0,
  "msg": "成功",
  "data": [
    {
      "ID": 1,
      "IconLink": "应用图标地址",
      "BundleIdentifier": "应用包名",
      "Name": "应用名称",
      "Version": "应用版本号",
      "BuildVersion": "应用BuildVersion",
      "MiniVersion": "最低支持ios版本",
      "Summary": "简介",
      "AppLink": "应用下载地址，iPhone使用Safari浏览器访问即可下载",
      "Size": "应用大小",
      "Count": "累计下载量"
    }
  ]
}
```

<img src="https://cdn.nlark.com/yuque/0/2021/png/1077776/1614159853374-673e82af-a2f2-479d-9ef8-03da193ed801.png#from=url&id=yGJKs&margin=%5Bobject%20Object%5D&originHeight=1970&originWidth=1154&originalType=binary&ratio=2&status=done&style=none" width="50%" height="50%">

