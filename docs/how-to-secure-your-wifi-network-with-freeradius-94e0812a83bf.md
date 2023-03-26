# 如何使用 Freeradius 保护您的 Wifi 网络

> 原文：<https://medium.com/hackernoon/how-to-secure-your-wifi-network-with-freeradius-94e0812a83bf>

![](img/c3f31d01f54d97085dd86d4a9500cc21.png)

在我们学校，我们有一个开放的无线网络，带有一个强制网络门户和另一个仅供教师使用的 WLAN (WPA Enterprise，802.1X)。对于这两个网络，我们都使用 RADIUS 服务器进行身份验证。Freeradius 是使用最广泛的开源 radius 服务器，我们也在使用。在本文中，我们希望为加密连接设置一个 Freeradius 服务器和证书。特别是，我想把重点放在与 linuxmuster.net 6.2 的连接和与 LDAP 服务器的认证上。

RADIUS 服务器通常负责三件事:认证、授权和计费(通常称为 AAA)。我们将只处理前两个“As”，即凭证是否正确以及用户是否被授权访问(例如，访问 Wifi)。

# 安装自由半径

我们在当前的 Linux 安装(这里是 Ubuntu 18.04 服务器)上执行安装，例如在 [LXD 容器](https://openschoolsolutions.org/5-reasons-use-lxd/)或虚拟机中。

```
$ apt install freeradius freeradius-ldap freeradius-utils
```

# 配置

## 基本配置

为了测试我们的 freeradius 服务器，我们在***/etc/freeradius/3.0/users***中注释掉以下行，或者将其插入文件的开头:

```
# Remove the “#” before the next line
steve Cleartext-Password := “testing”
```

默认情况下，文件***/etc/freeradius/3.0/clients . conf***应该包含本地主机作为客户端:

```
client localhost {
    ipaddr = 127.0.0.1
    secret = testing123
}
```

现在我们可以进行第一次测试。因此，我们停止 freeradius 服务，并在调试模式下手动重启它:

```
$ systemctl stop freeradius.service
$ freeradius -X
…
Ready to process request
```

重要的是，最后有“*准备好处理请求*”。

## 测试基本配置

接下来，我们检查我们的测试用户“Steve”是否可以登录到 RADIUS 服务器(最好是在新的/第二个终端中):

```
$ radtest steve testing 127.0.0.1 10 testing123
```

如果一切正常，我们应该得到以下答案:

```
Sent Access-Request Id 234 from 0.0.0.0:40302 to 127.0.0.1:1812 length 75
User-Name = “steve”
User-Password = “testing”
NAS-IP-Address = 10.18.10.60
NAS-Port = 10
Message-Authenticator = 0x00
Cleartext-Password = “testing”
Received Access-Accept Id 234 from 127.0.0.1:1812 to 0.0.0.0:0 length 20
```

我们获得*访问接受*是很重要的。如果此请求成功，则 freeradius 服务器基本上已设置完毕，并且以下身份验证过程应该正常工作，不会再出现任何问题:

*   奶头
*   无檐软平帽
*   MS-CHAPv1
*   MS-CHAPv2
*   PEAP
*   EAP-TTLS
*   EAP-GTC
*   EAP-MD5

这些方法是不同的“安全”协议。所有都使用用户名和密码进行身份验证。你可以在这里阅读更多关于不同的(P)EAP 方法[。MS-CHAPv1 和 v2 是微软的协议。](https://wiki.freeradius.org/protocol/EAP)

> **注意:**我们现在应该注释或删除带有我们的用户“Steve”的行！

在下文中，我们将配置 LDAP 模块并为 EAP-TTLS 创建新的证书。

## 设置 LDAP 连接

我们在文件***/etc/freeradius/3.0/MODS-enabled/LDAP***中配置 LDAP 服务器。如果这个文件不存在，我们必须首先创建一个符号链接。

```
$ cd /etc/freeradius/3.0/mods-enabled/
$ ln -s ../mods-available/ldap ./
```

在文件的开头，我们配置了 LDAP 服务器:

```
server = “ldaps://linuxmuster.internal.example.com”
identity = “cn=admin,dc=internal,dc=example,dc=com”
password = superSecretPassword
base_dn = “ou=accounts,dc=internal,dc=example,dc=com”
…
group {
    …
    membership_filter = “(|(member=%{control:Ldap-UserDn})(memberUid=%{%{Stripped-User-Name}:-%{User-Name}}))”
    …
}
```

您必须确保 RADIUS 和 LDAP 服务器之间通信所需的所有端口都是打开的。

> **注意:**如果您想将 Freeradius 3.0 与 linuxmuster.net 6.2 一起使用，您必须调整以下代码行，以便与 Windows 一起进行身份验证( […](https://wiki.freeradius.org/guide/certificate-compatibility) ):

```
update {
    control:Password-With-Header += ‘userPassword’
    control:NT-Password := ‘sambaNTPassword’
    …
}
```

## 测试 LDAP 连接

在 freeradius 中配置 LDAP 服务器后，我们必须重新启动 freeradius 一次，然后可以测试 LDAP 中的用户是否可以登录到 radius 服务器。

```
$ systemctl restart freeradius.service
$ radtest testuser password localhost 10 testing123
Sent Access-Request Id 213 from 0.0.0.0:46425 to 127.0.0.1:1812 length 73
User-Name = “testuser”
User-Password = “password”
NAS-IP-Address = 10.18.10.60
NAS-Port = 10
Message-Authenticator = 0x00
Cleartext-Password = “password”
Received Access-Accept Id 213 from 127.0.0.1:1812 to 0.0.0.0:0 length 20
```

如果我们再次收到“ *Received Access-Accept* ”作为回答，RADIUS 和 LDAP 服务器之间的连接正常。如果不行，我们应该手动启动 RADIUS 服务器，看看 RADIUS 服务器给我们什么错误。

```
$ systemctl stop freeradius.service
$ freeradius -X
…
Ready to process request
```

## 创建证书(针对 EAP-TTLS)

默认情况下，Freeradius 使用所谓的蛇油证书，这当然不是为了生产用途。因此，我们在下面的步骤中为服务器创建一个新的根 CA 和一个证书。您将在***/etc/freeradius/3.0/certs/***中找到证书和相应的配置文件。首先我们打开文件 ***ca.cnf*** 并更改一些设置:

```
…
[ CA_default ]
…
default_days = 3650
…
default_md = sha256
…[ req ]
…
default_bits = 2048
input_password = supersecretandlongpassword
output_password = supersecretandlongpassword
…[certificate_authority]
countryName = US
stateOrProvinceName = My State
localityName = My Town
organizationName = My School
emailAddress = admin@my-school.org
commonName = “CA Freeradius”
…
```

我们现在对文件 ***server.cnf*** 进行类似的设置:

```
…
[ CA_default ]
…
default_days = 3560
…
default_md = sha256
…[ req ]
…
default_bits = 2048
input_password = supersecretandlongpassword
output_password = supersecretandlongpassword[server]
countryName = US
stateOrProvinceName = My State
localityName = My Town
organizationName = My School
emailAddress = admin@my-school.org
commonName = “Freeradius Server Certificate”
```

现在我们还必须更改文件***/etc/freeradius/3.0/MODS-enabled/EAP***中的密码:

```
tls-config tls-common {
    private_key_password = supersecretandlongpassword
    …
}
```

我们用一个简单的`make`来创建证书:

```
$ cd /etc/freeradius/3.0/certs/
$ make
```

## 测试 EAP-TTLS 登录

## 编译 epol_test

`radtest`不支持 EAP-TTLS 认证测试。为此，我们需要工具`eapol_test`，它是 wpa_supplicant 包的一部分。可惜这个工具不是默认由 *wpa_supplicant* 构建的，所以我们得自己动手。我们下载源代码，将其解包，然后安装一些依赖项。

```
$ wget [https://w1.fi/releases/wpa_supplicant-2.7.tar.gz](https://w1.fi/releases/wpa_supplicant-2.7.tar.gz)
$ tar -xzvf wpa_supplicant-2.7.tar.gz
$ apt install build-essential pkg-config libnl-3-dev libssl-dev libnl-genl-3-dev
$ cd wpa_supplicant-2.7
$ cp defconfig .config
```

然后我们要打开文件**。配置**并找到行*#配置 _ EAPOL _ 测试=y* 并删除“#”。

```
$ nano .config
# Remove the “#”
CONFIG_EAPOL_TEST=y
```

我们使用以下命令构建程序，并将其复制到正确的位置:

```
$ make eapol_test
$ cp eapol_test /usr/local/bin
```

## 使用 eapol_test 测试登录

为了测试 EAP-TTLS，我们需要一个用于`eapol_test`的小配置文件，如下所示:

```
$ nano eapol_test.conf
network={
    ssid=”example”
    key_mgmt=WPA-EAP 
    eap=TTLS
    identity=”user”
    anonymous_identity=”anonymous”
    password=”supersecret”
    phase2=”auth=PAP”
}
```

现在我们可以称`eapol_test`:

```
$ eapol_test -c eapol_test.conf -a 127.0.0.1 -p 1812 -s testing123
```

如果你在最后得到这个问题，一切都是成功的:

```
MPPE keys OK: 1 mismatch: 0
SUCCESS
```

# 设置客户端(例如接入点)

到目前为止，我们只在 RADIUS 服务器上直接测试过。通常，身份验证请求来自接入点、强制网络门户或无线控制器。我们必须将它们设置为 RADIUS 服务器上的客户端。我们打开文件***/etc/freeradius/3.0/clients . conf***，插入所有接入点等。以他们的 IP 和密码/秘密结尾:

```
$ nano /etc/freeradius/3.0/clients.conf
client AP1 {
    ipaddr = 10.0.0.10
    secret = supersecretsecret
}
```

现在，您可以在接入点上设置 WPA Enterprise (802.1X)网络，并使用其 IP、端口(1812)和刚刚设置的密码/密码来配置 RADIUS 服务器。现在，您应该能够使用移动设备登录 WLAN 了。

# 限制对特定 LDAP 组的访问

在我们学校，只有员工和教师以及高中生可以访问学校的 WLAN。在 linuxmuster.net 有一群 *p_wifi* 。该组中的每个人都应该有权访问。到目前为止，我们的 RADIUS 服务器是以这样一种方式配置的，即每个用户都可以在 LDAP 中访问。为了限制访问，我们将下面几行添加到文件***/etc/freeradius/3.0/users***:

```
DEFAULT Ldap-Group == “cn=p_wifi,ou=groups,dc=internal,dc=example,dc=com”
DEFAULT Auth-Type := Reject
    Reply-Message = “Your are not allowed to access the WLAN!”
```

# 半径和 linuxmuster.net 6.2

文档中描述了 linuxmuster.net 6.2 版 Freeradius 的安装[。除了几个细节，配置都很像。在 Freeradius 2.0 中，证书的默认设置不再是最新的，因此某些客户端可能会出现连接问题(例如，当前的 macOS)。这里你一定要用 Freeradius 3.0 的默认值(见上图)！](http://docs.linuxmuster.net/en/latest/systemadministration/network/radius/ldap.html)

## 根据无线局域网络不同的接入限制

在我们学校，我们有一个面向来宾和学生的专属门户，以及一个面向员工和教师的 WPA 802.1X(企业)网络。虽然 p_wifi 组中的每个人都可以登录到强制网络门户，但只有教师和工作人员可以登录到 WPA 802.1X 网络。来自强制网络门户的请求来自与 WPA 企业网络不同的 IP ( [pfSense](https://openschoolsolutions.org/tag/pfsense/) )。因此，在文件***/etc/freeradius/sites-enabled/inner-tunnel***中，我们包含了一个条件，用于检查请求来自哪个 IP，并相应地决定某人是否可以访问:

```
post-auth {
    …
    #Only allow Teacher&Staff on WPA 802.1X Teacher and Staff network
    if (NAS-IP-Address == 10.0.0.10) {
        if (LDAP-Group == “cn=teachers,ou=groups,dc=internal,dc=example,dc=com” || LDAP-Group == “cn=staff,ou=groups,dc=internal,dc=example,dc=com”) {
            noop
        } else {
            reject
        }
    }
    …
}
```

# 结论

本文仅描述了许多可能配置中的一种。RADIUS 服务器很复杂，但是由于 freeradius 良好的标准配置(尤其是在版本 3 中)，您很快就会成功。很长一段时间，我们只使用强制网络门户，它工作得很好，但随着 WPA 802.1X(企业)网络的使用，可用性和安全性有所提高。没有 RADIUS 服务器，这是不可能的。

有用的链接:

*   [http://deployingradius.com/](http://deployingradius.com/)
*   https://wiki.freeradius.org/guide/HOWTO

【openschoolsolutions.org】最初发表于[](https://openschoolsolutions.org/freeradius-secure-wifi-network/)**。**