# Shiro漏洞分析合集

## shiro 命令回显示

### 靶场搭建

```yaml
version: '2'
services:
 web:
   image: vulhub/shiro:1.2.4
   ports:
    - "8080:8080"
```
启动docker `docker-compose up`

### 漏洞分析

shiro在漏洞利用的时候，需要在header头发送payload，反序列化的poc数据包超过中间件的`maxHeaderSize`的时候，就会返回错误。

各类中间件默认配置的header头大小

| 中间价 | Header Size        |
| ------ | ------------------ |
| Apache | 8K                 |
| IIS    | 16K                |
| Nginx  | 4-8k               |
| Tomcat | 8-48K ( <7.0 48k ) |

`tomcat-coyote.jar!/org/apache/coyote/http11/AbstractHttp11Protocol.class`

```java
public abstract class AbstractHttp11Protocol<S> extends AbstractProtocol<S> {
    private int socketBuffer = 9000;
    private int maxSavePostSize = 4096;
    private int maxHttpHeaderSize = 8192; // 限制了header头大小
    private int connectionUploadTimeout = 300000;
    private boolean disableUploadTimeout = true;
    private String compression = "off";
    private String noCompressionUserAgents = null;
    private String compressibleMimeTypes = "text/html,text/xml,text/plain,text/css,text/javascript,application/javascript";
    private int compressionMinSize = 2048;
    private String restrictedUserAgents = null;
    private String server;
    private int maxTrailerSize = 8192;
    private int maxExtensionSize = 8192;
    private int maxSwallowSize = 2097152;
    private boolean secure;
    private int upgradeAsyncWriteBufferSize = 8192;
    private Set<String> allowedTrailerHeaders = Collections.newSetFromMap(new ConcurrentHashMap());
```

