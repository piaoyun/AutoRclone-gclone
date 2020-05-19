# 准备工作
- 一台 VPS服务器，当然在 本地电脑 Windows 上也是可以的，不过本文以 VPS 为例
- 一个谷歌账号
- 一个 Google Team Drive 账号，如果没有，本文结尾会提供几个免费获取渠道

# 大致过程
- [x] 安装 Python3 和 AutoRclone
- [x] 借助谷歌开发者平台，生成服务账号
- [ ] 将服务账号 service accounts 加入到 Google Groups
- [ ] 安装 gclone 进行复制文件

# 一、安装详细过程
以下教程内容基于 Ubuntu 18.04 Lts 系统的 VPS 服务器
## 1.安装Python3

`apt-get install git python3 python3-pip -y`

## 2.安装AutoRclone

```python
git clone https://github.com/xyou365/AutoRclone && cd AutoRclone && pip3 install -r requirements.txt
```

## 3.Windows 系统安装：
安装最新的[Rclone](https://rclone.org/downloads/ "Rclone")，直接从Github下载[AutoRclone](https://github.com/xyou365/AutoRclone "AutoRclone")后，在Windows的`cmd`或`PowerShell`中切换到`AutoRclone`的目录，输入以下命令安装相关Python依赖包

`pip3 install -r requirements.txt`

# 二、生成Service Accounts

首先开启[Drive API](https://developers.google.com/drive/api/v3/quickstart/python "Drive API")：[https://developers.google.com/drive/api/v3/quickstart/python](https://developers.google.com/drive/api/v3/quickstart/python "https://developers.google.com/drive/api/v3/quickstart/python")，并将`credentials.json`下载保存到你的`AutoRclone`目录下面

![](https://img.vim-cn.com/46/a41db9c3b2b25ef86f5c55db3778544d854580.jpg)

**开启Drive Api后分三种情况**

### 1.如果你之前没创建过项目, 直接运行

```bash
python3 gen_sa_accounts.py --quick-setup 5
```
+ 以上命令含义：
    + 创建6个项目（项目0到项目5）
    + 开启相关的服务
    + 创建600个service accounts（6个项目，每个项目100个）
    + 将600个service accounts的授权文件下载到accounts文件夹下面

### 2.如果你已经有N个项目，现需要创建新的项目并在新的项目中创建service accounts，直接运行

```bash
python3 gen_sa_accounts.py --quick-setup 2 --new-only
```
+ 以上命令含义：
    + 额外创建2个项目（项目N+1到项目N+2）
    + 开启相关的服务
    + 创建200个service accounts（2个项目，每个项目100个）
    + 将200个service accounts的授权文件下载到accounts文件夹下面

### 3.如果你想用已有的项目来创建service accounts（不创建新的项目），直接运行

```bash
python3 gen_sa_accounts.py --quick-setup -1
```

注意这会覆盖掉已有的`service accounts`

顺利完成后，AutoRclone文件下面的accounts文件夹下会有很多的json文件。


# 三、将service accounts加入Google Groups

为了方便管理service accounts，也是为了让我们的Team Drive可以容纳更多的service accounts，我们这里用到了Google Groups。

[Official limits to the members of Team Drive](https://support.google.com/a/answer/7338880?hl=en "Official limits to the members of Team Drive") (Limit for individuals and groups directly added as members is 600).


### 1.对于G Suite管理员
按照[官方步骤](https://developers.google.com/admin-sdk/directory/v1/quickstart/python "官方步骤")，开启Directory API，将生成的json文件保存到credentials文件下。
在[控制面版](https://support.google.com/a/answer/33343?hl=en "控制面版")里面创建一个群组，创建好你会获得一个类似域名邮箱的地址sa@yourdomain.com
利用API将service accounts加入Google Groups

```bash
python3 add_to_google_group.py -g sa@yourdomain.com
```

如果想看参数的具体含义，直接运行`python3 add_to_google_group.py -h`

### 2.对于普通Google账号
直接创建一个[Google Group](https://groups.google.com/ "Google Group")
然后手动地将service accounts对应的邮箱地址（可以在json认证文件中找到）挨个加进去。**但每次只能加10个，每24小时只能加100个。**

## 批量提取 Service Accounts
以下是网友提供的提取方法：
生成 Service Accounts 后，直接运行以下命令：

```bash
cat ~/AutoRclone/accounts/*.json | grep "client_email" | awk '{print $2}'| tr -d ',"' | sed 'N;N;N;N;N;N;N;N;N;/^$/d;G' > ~/email.txt
```
批量提取代码来源于：http://fxxkr.com/2020/04/06/onekey-print-email-from-google-sa-json/

运行后，会自动提取 Service Accounts 邮箱账户，并保存到目录下的 email.txt 文件中，同时还很贴心地每10个邮箱账户就隔开一行，方便复制。




