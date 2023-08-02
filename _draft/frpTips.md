### 服务的配置
测试版本为Windows11_64位 0.49和0.51.1
```
[common]
bind_addr = 0.0.0.0
bind_port = 7000

kcp_bind_port = 7000

dashboard_addr = 0.0.0.0
dashboard_port = 7500

dashboard_user = admin
dashboard_pwd = admin

token = 12345678

tls_only = true

tls_cert_file = server.crt
tls_key_file = server.key
tls_trusted_ca_file = ca.crt
```
### 客户端配置
`server_addr`改成服务器IP
```
[common]
server_addr = 0.0.0.0
server_port = 7000

token = 12345678

admin_addr = 127.0.0.1
admin_port = 7400
admin_user = admin
admin_pwd = admin

tls_enable = true

tls_cert_file = client.crt
tls_key_file = client.key
tls_trusted_ca_file = ca.crt

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6001
```
### 配置双向认证
#### CA配置文件
```
cat > my-openssl.cnf << EOF
[ ca ]
default_ca = CA_default
[ CA_default ]
x509_extensions = usr_cert
[ req ]
default_bits        = 2048
default_md          = sha256
default_keyfile     = privkey.pem
distinguished_name  = req_distinguished_name
attributes          = req_attributes
x509_extensions     = v3_ca
string_mask         = utf8only
[ req_distinguished_name ]
[ req_attributes ]
[ usr_cert ]
basicConstraints       = CA:FALSE
nsComment              = "OpenSSL Generated Certificate"
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid,issuer
[ v3_ca ]
subjectKeyIdentifier   = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints       = CA:true
EOF
```
#### 生成默认CA
```
openssl genrsa -out ca.key 2048
openssl req -x509 -new -nodes -key ca.key -subj "/CN=example.ca.com" -days 5000 -out ca.crt
```
#### 生成 frps 证书
IP需要改成自己服务器的IP 其他可以不动
```
openssl genrsa -out server.key 2048

openssl req -new -sha256 -key server.key \
    -subj "/C=XX/ST=DEFAULT/L=DEFAULT/O=DEFAULT/CN=server.com" \
    -reqexts SAN \
    -config <(cat my-openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:localhost,IP:127.0.0.1,DNS:example.server.com")) \
    -out server.csr

openssl x509 -req -days 365 -sha256 \
	-in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
	-extfile <(printf "subjectAltName=DNS:localhost,IP:127.0.0.1,DNS:example.server.com") \
	-out server.crt
```
#### 生成 frpc 的证书
默认就行
```
openssl genrsa -out client.key 2048
openssl req -new -sha256 -key client.key \
    -subj "/C=XX/ST=DEFAULT/L=DEFAULT/O=DEFAULT/CN=client.com" \
    -reqexts SAN \
    -config <(cat my-openssl.cnf <(printf "\n[SAN]\nsubjectAltName=DNS:client.com,DNS:example.client.com")) \
    -out client.csr

openssl x509 -req -days 365 -sha256 \
    -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
	-extfile <(printf "subjectAltName=DNS:client.com,DNS:example.client.com") \
	-out client.crt
```
