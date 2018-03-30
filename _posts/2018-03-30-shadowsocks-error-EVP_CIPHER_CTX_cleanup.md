---
title: shadowsocks2.8.2启动报undefined symbol: EVP_CIPHER_CTX_cleanup错误
categories: youknow
tags: 
 - youknow
 - linux
---

#shadowsocks2.8.2启动报undefined symbol: EVP_CIPHER_CTX_cleanup错误

> 由于openssl升级到1.1.0以上版本，导致shadowsocks2.8.2启动报undefined symbol: EVP_CIPHER_CTX_cleanup错误

<!-- more -->

## 报错如下

```bash
INFO: loading config from /etc/shadowsocks/config.json
2018-03-12 17:52:29 INFO     loading libcrypto from libcrypto.so.1.1
Traceback (most recent call last):
  File "/usr/local/bin/ssserver", line 11, in <module>
    sys.exit(main())
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/server.py", line 34, in main
    config = shell.get_config(False)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py", line 262, in get_config
    check_config(config, is_local)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py", line 124, in check_config
    encrypt.try_cipher(config['password'], config['method'])
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 44, in try_cipher
    Encryptor(key, method)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 83, in __init__
    random_string(self._method_info[1]))
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 109, in get_cipher
    return m[2](method, key, iv, op)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py", line 76, in __init__
    load_openssl()
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py", line 52, in load_openssl
    libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,)
  File "/usr/lib/python2.7/ctypes/__init__.py", line 375, in __getattr__
    func = self.__getitem__(name)
  File "/usr/lib/python2.7/ctypes/__init__.py", line 380, in __getitem__
    func = self._FuncPtr((name_or_ordinal, self))
```


## 解决办法

1.`sudo vim /home/heimo/.local/lib/python2.7/site-packages/shadowsocks/crypto/openssl.py`


所有`EVP_CIPHER_CTX_cleanup`改为`EVP_CIPHER_CTX_reset`


2.`sudo vim /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py`


所有`EVP_CIPHER_CTX_cleanup`改为`EVP_CIPHER_CTX_reset`