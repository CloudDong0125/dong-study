#### **1. 强制忽略证书验证（通过环境变量）**

```bash
# 临时设置（当前终端会话有效）
export SVN_SSL_SERVER_CERT_POLICY=allow

# 执行 SVN 命令
svn checkout https://192.168.1.200/svn/aoyexm/aoyeAppOH

# 或者合并为一行命令
SVN_SSL_SERVER_CERT_POLICY=allow svn checkout https://192.168.1.200/svn/aoyexm/aoyeAppOH
```

![image-20250528130631939](./assets/image-20250528130631939.png)

```bash

```

