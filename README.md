ProfileSigner
=======

ProfileSigner是一个Python脚本，它将加密和/或签名.mobileconfig配置文件。

目前，这只对存储在密钥链中的证书有效。

## 如何使用

```bash
./profile_signer.py -h
usage: profile_signer.py [-h] [-k KEYCHAIN] -n NAME
                         {sign,encrypt,both} infile outfile

Sign or encrypt mobileconfig profiles, using either a cert + key file, or a
keychain certificate.

positional arguments:
  {sign,encrypt,both}   Choose to sign, encrypt, or do both on a profile.
  infile                Path to input .mobileconfig file
  outfile               Path to output .mobileconfig file. Defaults to
                        outputting into the same directory.

optional arguments:
  -h, --help            show this help message and exit

Keychain arguments:
  Use these if you wish to sign with a Keychain certificate.

  -k KEYCHAIN, --keychain KEYCHAIN
                        Name of keychain to search for cert. Defaults to
                        login.keychain
  -n NAME, --name NAME  Common name of certificate to use from keychain.
```

注意，infile和outfile**必须**以不同的名称命名。不能用输出文件覆盖infile。

### 签署一份概要

如果我想用苹果开发者网站上的“第三方Mac开发者应用程序”证书签署一个文件，例如我的[AcrobatPro.mobileconfig](https://github.com/nmcspadden/Profiles/blob/master/AcrobatPro.mobileconfig)，我会使用这个命令:
' ./ profile_siger .py -n "第三方Mac开发者应用程序"签署AcrobatPro。mobileconfig AcrobatProSigned.mobileconfig”

签署一个概要文件将会把系统首选项的概要文件窗格中概要文件名称下面的红色“未验证”文本变成绿色的“已验证”文本。在不破坏签名验证的情况下，不能篡改签名配置文件。

注意，无论您用什么证书签署您的配置文件，都必须信任您的客户端，否则他们将拒绝安装它，没有警告消息。

### 加密配置文件

如果我想用相同的证书加密上面的配置文件，我会使用这个命令:
' ./profile_signer.py -n "3rd Party Mac Developer Application" sign AcrobatPro.mobileconfig AcrobatProSigned.mobileconfig'

加密的配置文件(未签名)将有加密的有效负载。虽然整个概要文件结构以XML的形式可见(并且可以修改)，但有效负载本身将使用您选择的证书**的**公钥加密。

注意，要安装概要文件的客户机机器必须拥有与**签署的证书的**私钥，以便解密概要文件。如果客户端不能解密配置文件，它就不能安装它。

如果您想加密一个配置文件，并确保只有一个特定的客户端能够接收/使用它，那么您需要使用该客户端的特定机器证书来加密它——而该设置已经超出了本项目的范围。您可以使用类似于Puppet certs的东西。

### 加密和签名配置文件

要同时进行加密和签名，请使用以下命令:
' ./profile_signer.py -n "3rd Party Mac Developer Application" both AcrobatPro.mobileconfig AcrobatProBoth.mobileconfig'

应用与上面相同的规则——使用公钥加密意味着必须在客户机上使用私钥解密。对配置文件进行签名意味着如果证书不受信任，客户端将会警报。