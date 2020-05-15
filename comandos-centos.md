Comandos usados na instalação do Nginx compilando código fonte na distribuição CentOS.

```shell
yum update -y
```
```shell
yum install epel-release vim curl wget tree perl perl-devel perl-ExtUtils-Embed libxslt libxslt-devel libxml2 libxml2-devel gd gd-devel
```
```shell
yum groupinstall -y 'Development Tools'
```
```shell
wget ftp://ftp.pcre.org/pub/pcre/pcre-8.44.tar.gz
```
```shell
tar xzvf pcre-8.44.tar.gz
```
```shell
wget http://zlib.net/zlib-1.2.11.tar.gz
```
```shell
tar xzvf zlib-1.2.11.tar.gz
```
```shell
wget https://www.openssl.org/source/openssl-1.1.1f.tar.gz
```
```shell
tar xzvf openssl-1.1.1f.tar.gz
```
```shell
wget http://nginx.org/download/nginx-1.16.1.tar.gz
```
```shell
tar xzvf nginx-1.16.1.tar.gz
```
```shell
rm -rf /root/*.tar.gz
```
```shell
cp /root/nginx-1.16.1/man/nginx.8 /usr/share/man/man8/
```
```shell
gzip /usr/share/man/man8/nginx.8
```
```shell
cd /root/nginx-1.16.1/
```
```shell
./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --user=nginx --group=nginx --build=CentOS --builddir=nginx-1.16.1 --with-select_module --with-poll_module --with-threads --with-file-aio --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module=dynamic --with-http_image_filter_module=dynamic --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_auth_request_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_slice_module --with-http_stub_status_module --with-http_perl_module=dynamic --with-perl_modules_path=/usr/lib64/perl5 --with-perl=/usr/bin/perl --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --with-mail=dynamic --with-mail_ssl_module --with-stream=dynamic --with-stream_ssl_module --with-stream_realip_module --with-stream_ssl_preread_module --with-compat --with-pcre=../pcre-8.44 --with-pcre-jit --with-zlib=../zlib-1.2.11 --with-openssl=../openssl-1.1.1f --with-openssl-opt=no-nextprotoneg --with-debug
```
```shell
make
```
```shell
make install
```
```shell
ln -s /usr/lib64/nginx/modules /etc/nginx/modules
```
```shell
nginx -V
```
```shell
useradd --system --home /var/cache/nginx --shell /sbin/nologin --comment "nginx user" --user-group nginx
```
```shell
nginx -t
```
```shell
mkdir -p /var/cache/nginx/client_temp /var/cache/nginx/fastcgi_temp /var/cache/nginx/proxy_temp /var/cache/nginx/scgi_temp /var/cache/nginx/uwsgi_temp
```
```shell
chmod 700 /var/cache/nginx/*
```
```shell
chown nginx:root /var/cache/nginx/*
```
```shell
nginx -t
```
```shell
vim /etc/systemd/system/nginx.service
```
```ini
[Unit]
Description=nginx - high performance web server
Documentation=https://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
```

```shell
systemctl enable nginx.service
```
```shell
systemctl start nginx.service
```
```shell
systemctl status nginx.service
```
```shell
systemctl stop firewalld.service
```
```shell
systemctl disable firewalld.service
```
```shell
mkdir /etc/nginx/{conf.d,snippets,sites-available,sites-enabled}
```
```shell
chmod 640 /var/log/nginx/*
```
```shell
chown nginx:adm /var/log/nginx/access.log /var/log/nginx/error.log
```
```shell
vim /etc/logrotate.d/nginx
```
```ini
/var/log/nginx/*.log {
    daily
    missingok
    rotate 52
    compress
    delaycompress
    notifempty
    create 640 nginx adm
    sharedscripts
    postrotate
        if [ -f /var/run/nginx.pid ]; then
            kill -USR1 `cat /var/run/nginx.pid`
        fi
    endscript
}
```
```shell
cd /root/
```
```shell
rm -rf nginx-1.16.1/ openssl-1.1.1f/ pcre-8.44/ zlib-1.2.11/
```