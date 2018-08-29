# mkcert（证书制作）

mkcert是一个零配置、制作本地信任https开发证书的工具.

```
$ mkcert -install
Created a new local CA at "/Users/filippo/Library/Application Support/mkcert" 💥
The local CA is now installed in the system trust store! ⚡️
The local CA is now installed in the Firefox trust store (requires restart)! 🦊

$ mkcert example.com '*.example.org' myapp.dev localhost 127.0.0.1 ::1
Using the local CA at "/Users/filippo/Library/Application Support/mkcert" ✨

Created a new certificate valid for the following names 📜
 - "example.com"
 - "*.example.org"
 - "myapp.dev"
 - "localhost"
 - "127.0.0.1"
 - "::1"

The certificate is at "./example.com+5.pem" and the key at "./example.com+5-key.pem" ✅
```

<p align="center"><img width="444" alt="Chrome screenshot" src="https://user-images.githubusercontent.com/1225294/41887838-7acd55ca-78d0-11e8-8a81-139a54faaf87.png"></p>

使用来自真实机构的证书 (CAs)开发是一件危险或者不可能的事情，  (比如主机是 `localhost` 或者 `127.0.0.1`), 自签名证书也有可能导致信任错误. 管理自己的 CA 是最好的解决办法,但是通常涉及到晦涩的命令，专业知识和手动步骤。
mkcert 通过系统root自动创建和安装一个本地CA  ,并且自动生成一个可信任的证书的工具

## 安装

> **警告**:   mkcert自动生成的文件`rootCA-key.pem`提供了完整的功能来拦截来自您机器的安全请求。 不要分享它。

### macOS

在MacOs 使用 [Homebrew](https://brew.sh/)

```
brew install mkcert
brew install nss # if you use Firefox
```

或者 MacPorts.

```
sudo port sync
sudo port install mkcert
```

### Linux

在Linux, 首先安装 `certutil`.

```
sudo apt install libnss3-tools
    -or-
sudo yum install nss-tools
    -or-
sudo pacman -S nss
```

然后你可以安装并使用 [Linuxbrew](http://linuxbrew.sh/)

```
brew install mkcert
````

或者直接从源码安装 (requires Go 1.10+)

```
go get -u github.com/FiloSottile/mkcert
$(go env GOPATH)/bin/mkcert
```

或者使用 [the pre-built binaries](https://github.com/FiloSottile/mkcert/releases).

在ArchLinux也可以使用 [AUR helper](https://wiki.archlinux.org/index.php/AUR_helpers) to install mkcert from the [PKGBUILD](https://aur.archlinux.org/packages/mkcert/).

```
yaourt -S mkcert
```

### Windows

在windows上, 使用 Chocolatey

```
choco install mkcert
```

或者从源码安装 (需要 Go 1.10+), 使用 [the pre-built binaries](https://github.com/FiloSottile/mkcert/releases).

## Supported root stores

mkcert supports the following root stores:

* macOS system store
* Windows system store
* Linux variants that provide either
    * `update-ca-trust` (Fedora, RHEL, CentOS) or
    * `update-ca-certificates` (Ubuntu, Debian) or
    * `trust` (Arch)
* Firefox (macOS and Linux only)
* Chrome and Chromium
* Java (when `JAVA_HOME` is set)

## 高级主题

### 移动设备

为了能让证书被移动设备信任， 你需要安装 root CA. 这是一个在文件夹中被 `mkcert -CAROOT`输出的`rootCA.pem` 文件.

在iOS上，您可以使用AirDrop，通过电子邮件将CA发送给自己，也可以从HTTP服务器提供。 安装后，你必须[开启完全信任](https://support.apple.com/en-nz/HT204477). **注意**: 早期版本的mkcert会有一个 [iOS bug](https://forums.developer.apple.com/thread/89568),如果在“证书信任设置”中看不到root，则可能需要更新mkcert 和 [重新生成 root](https://github.com/FiloSottile/mkcert/issues/47#issuecomment-408724149).

Android需要安装 CA 并在你的开发构建的app开启用户权限 详见 [this StackOverflow answer](https://stackoverflow.com/a/22040887/749014).

### 修改本地的CA文件

CA证书及其密钥储存在的应用程序数据文件夹中。你通常不用担心它，因为安装是自动化的，但是这个位置是用“MKCIET-CAROOT”输出的。
如果希望管理单独的CA，可以使用环境变量`$CAROOT'去设置mkcert将要放置并查找的本地CA文件的文件夹。

### 安装CA证书在其他系统

在信任库中安装不需要CA密钥，因此您可以导出CA证书并使用mkcert将其安装在其他计算机上。

* 在 `mkcert -CAROOT`寻找`rootCA.pem` 
* 复制到其他机器
* 在他的路径设置 `$CAROOT` 
* 运行 `mkcert -install`

请记住，mkcert用于开发目的，而非生产，因此不应在最终的用户的计算机上使用，并且您不应*导出或共享`rootCA-key.pem`。
