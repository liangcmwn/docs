### 一. 生成证书
```
# 1、首先，进入你想创建证书和私钥的目录，例如：
cd /etc/nginx/

# 2、创建服务器私钥，命令会让你输入一个口令：
openssl genrsa -des3 -out server.key 1024

# 3、创建签名请求的证书（CSR）：
openssl req -new -key server.key -out server.csr

# 4、在加载SSL支持的Nginx并使用上述私钥时除去必须的口令：
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key


# 5、最后标记证书使用上述私钥和CSR：
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```
## 二. 配置 Nginx
```
server {
    listen       443;
    server_name  本机的IP地址;

    ssl                  on;
    ssl_certificate      /etc/nginx/server.crt;
    ssl_certificate_key  /etc/nginx/server.key;

    ssl_session_timeout  5m;

#    ssl_protocols  SSLv2 SSLv3 TLSv1;
#    ssl_ciphers  ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
#    ssl_prefer_server_ciphers   on;

    location / {
        #root   html;
        #index  testssl.html index.html index.htm;
     proxy_redirect off;
     proxy_set_header Host $host;
     proxy_set_header X-Real-IP $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_pass http://IP地址/ssl/;
    }
}
```

## 三.ciphers 配置
CloudFlare 和 Mozilla 的 NGINX SSL 配置段
https://github.com/cloudflare/sslconfig/blob/master/conf
```
ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5; 
ssl_prefer_server_ciphers on;
```
https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
```
ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
ssl_ciphers ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS; 
ssl_prefer_server_ciphers on;
```
专业配置建议
其中含有大多数服务器的配置建议。 
Cipherli.st Strong Ciphers for Apache, nginx and Lighttpd

https://cipherli.st/

加密套件解读： 
ECDHE-RSA-AES128-GCM-SHA256 为例

ECDHE：秘钥交换算法 
RSA：签名算法 
AES128：对称加密算法 
GCM-SHA256：签名算法

默认项： 
秘钥交换算法：RSA 
签名算法：RSA 
模式：CBC 
AES256-SHA256 也就是 RSA-RSA-AES256-CBC-SHA256

