# Some SSH Commands
Some SSH Commands OS : Ubuntu 22.04

#### 2 sunucu arasında ssh ile dosya taşıma
~~~ ssh
sudo scp -r -P22 /var/www/html/* [target_user]@[target_server_ip]:/var/www/html/tasima_yedekleri/
~~~

#### Dosyalar arasında arama yapma
~~~ ssh
find /var/www/html/[search_folder_path]/ -name "*.[search_extension]" -exec grep -l "[search_text]" {} \;
~~~

#### İşletim sistemi bilgilerini görüntüleme
~~~ ssh
cat /etc/os-release
~~~

#### Klasör ve klasörün altındaki dosyaların sahipliğini değiştirme
~~~ ssh
sudo chown -R [user]:[user] /var/www/html/[folder]/
~~~

#### Klasör ve klasörün altındaki dosyaların iznini değiştirme
~~~ ssh
sudo chmod -R 0777 /var/www/html/[folder]/
~~~

#### Klasör ve klasörün altındaki dosyaları kopyalama
~~~ ssh
cp -R [folder]/* [target_folder]/
~~~

#### Dosyanın adını değiştirme
~~~ ssh
mv old_file.php new_file.php
~~~

#### Çalışan Servisleri Listeleme
~~~ ssh
systemctl list-units --all --type=service --no-pager | grep running
~~~

#### Dosyanın Yolunu Bulma
~~~ ssh
find / -iname info.php
~~~

#### MySQL Kullanıcı İşlemleri
*Root ile mysqle bağlanıyoruz*
~~~ ssh 
mysql -u root -p
~~~

*Kullanıcıyı oluşturuyoruz*
~~~ ssh 
CREATE USER 'orhancelik'@'%' IDENTIFIED BY '12345';
~~~

*Kullanıcıları kontrol ediyoruz*
~~~ ssh 
SELECT * FROM mysql.user;
~~~

*Sadece belirli yetkileri vermek istersek*
~~~ ssh 
GRANT SELECT, INSERT ON [veritabanı adı yada tüm veritabanı (*)].* TO 'orhancelik'@'%';
~~~

*Tüm yetkileri vermek istersek*
~~~ ssh 
GRANT ALL ON *.* TO 'orhancelik'@'%' WITH GRANT OPTION;
~~~

*Verdiğimiz izinleri silmek istersek*
~~~ ssh 
REVOKE SELECT, INSERT ON *.* FROM 'orhancelik'@%;
~~~

*Kullanıcı parolasını değiştirmek istersek* (Versiyona göre değişiyor)
~~~ ssh 
SELECT version();
~~~

*MySQL 5.76 üzerindeyse*
~~~ ssh 
ALTER USER 'orhancelik'@'%' IDENTIFIED BY 'yeni şifre';
~~~

*MySQL 5.76 altındaysa*
~~~ ssh 
SET PASSWORD FOR 'orhancelik'@'%' = PASSWORD('yeni şifre');
~~~

*Kullanıcıyı yetkilerini görmek istersek*
~~~ ssh 
SHOW GRANTS FOR 'orhancelik'@'%';
~~~

*Kullanıcıyı silmek istersek*
~~~ ssh 
DROP USER 'orhancelik'@'%';
~~~

*Değişiklikleri anında uyguluyoruz ve çıkış yapıyoruz*
~~~ ssh 
FLUSH PRIVILEGES;
~~~

~~~ ssh 
\q
~~~

*DB ve DB deki tablo boyularını görmek istersek*

~~~ ssh 
SELECT TABLE_SCHEMA AS `Database`, 
ROUND(SUM(DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024, 2) AS `Size (MB)` 
FROM information_schema.TABLES
WHERE TABLE_SCHEMA='db_name';
~~~

~~~ ssh 
SELECT TABLE_SCHEMA AS `Database`,
TABLE_NAME AS `Table`,
ROUND((DATA_LENGTH + INDEX_LENGTH) / 1024 / 1024) AS `Size (MB)`
FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'db_name'
ORDER BY (DATA_LENGTH + INDEX_LENGTH) DESC
~~~

#### MySQL Yedek Alma
~~~ ssh 
mysqldump --routines=true --triggers --events -u root -p [db_name] --ignore-table=[table_name_1] --ignore-table=[table_name_2] > export_db.sql
~~~

#### PFX Sertifika Çıkartıp Sunucuya Yükleme
~~~ ssh 
OS : Ubuntu
VH : Apache

// Pfx dosyasını sunucuya atıyoruz, /etc/ssl/<certificate_name>.pfx.

// Private Key oluşturuyoruz.
openssl pkcs12 -in "<certificate_name>.pfx" -nocerts -out private.key -nodes

// Sertifikayı oluşturuyoruz.
openssl pkcs12 -in "<certificate_name>.pfx" -nokeys -out certificate.crt

// Virtual Host ayarlarımızın olduğu dosyayı açıyoruz.
nano /etc/apache2/mods-enabled/<your-site>.conf

// Private Key ve SSL yollarınızı apacheye veriyoruz.
SSLCertificateFile /etc/ssl/<certificate_name>.pfx.
SSLCertificateKeyFile /etc/ssl/<certificate_key>.pfx.

// Apacheye yeniden başlatıp çıkıyoruz.
sudo service apache2 restart && exit
~~~

#### Linux üzerinde shell script yazma
~~~ ssh 
OS : Ubuntu

// SENARYO : 
// Node JS ile çalışan bir servisimiz var, ve sunucu reboot yada restart olduğu zaman bu servisin otomatik olarak ayağa kaldırılmasını istiyoruz.
// Aksi takdirde herhangi bir sebepten dolayı sunucu yeniden başlatılırsa bu tarz servisleri ayağa kaldırmayı unutuyoruz ve istek atıldığı zaman erişim sağlanamıyor.

// bash kodlarımızı düzenli bir yerde tutmak için shell_scripts adında bir klasör oluşturuyoruz.
mkdir shell_scripts

// Oluşturduğumuz klasörün içine giriyoruz.
cd shell_scripts

// Nano yardımıyla bir bash script dosyası oluşturuyoruz.
nano up_pm2_node_service.sh

// İçerisine aşağıdaki scripti ekliyoruz.
#!/bin/bash
up_pm2_node_service(){
  pm2 start /var/www/html/cron/server.js
}
up_pm2_node_service

// Kaydediyoruz
Ctrl + X => ardından (y) tuşuna basıyoruz.

// Yetkilerini ayarlıyoruz.
chmod u+x up_pm2_node_service.sh

// Script hazır şimdi bu shell scriptini sunucu reboot olduğunda otomatik çalışmasını ayarlayacağız.
// Ben bunu crontab ile yapıyorum.

// Crontab açıyoruz.
crontab -e

// İçerisine aşağıdaki satırı ekliyoruz.
@reboot /shell_scripts/up_pm2_node_service.sh

// Kaydediyoruz
Ctrl + X => ardından (y) tuşuna basıyoruz.

// Apacheye yeniden başlatıp çıkıyoruz.
sudo service apache2 restart && exit
~~~


#### APACHE 2.4.52 to 2.4.57 (Apache Versiyonu Yükseltme)
~~~ ssh
apache2 -v
sudo add-apt-repository ppa:ondrej/apache2
sudo apt update
sudo apt install apache2
apache2 -v
sudo systemctl restart apache2
~~~


#### Postgresql kullanıcı işlemleri
~~~ ssh
psql -U postgres
CREATE USER [kullanici_adi] WITH PASSWORD 'şifre';
GRANT CONNECT ON DATABASE [veritabanı_adı] TO [kullanici_adi];
\c [veritabanı_adı]
GRANT SELECT ON ALL TABLES IN SCHEMA public TO [kullanici_adi];
GRANT INSERT ON TABLE [tablo_adı] TO [kullanici_adi];
GRANT USAGE, SELECT, UPDATE ON SEQUENCE [seqans_adı] TO [kullanici_adi];
~~~


#### 🚀 Aynı Sunucuda Birden Fazla Docker Container ile Web Sitesi Yönetimi

Bu `docker-compose` dosyası, aynı sunucuda birden fazla web sitesini yönetmek için **Nginx Reverse Proxy** ve **Let's Encrypt otomatik SSL sertifikası** kurulumunu sağlar. Bu yapı GitHub ve Markdown uyumlu şekilde yazılmıştır.

### ⚙️ Servisler

- **nginx-proxy:**
  - Tüm gelen HTTP/HTTPS trafiğini ilgili web sitelerine yönlendirir.
  - Container adı: `nginx-proxy`
  - Portlar: 80 ve 443
  - Volumes:
    - `/var/run/docker.sock` → Docker container bilgilerini okumak için gerekli.
    - `./certs` → SSL sertifikalarını depolar.
    - `./vhost.d` → Virtual host konfigürasyonlarını saklar.
    - `./html` → Varsayılan web içeriği.
  - Network: `proxy_net`

- **letsencrypt (acme-companion):**
  - Nginx proxy ile entegre çalışarak sitelere otomatik SSL sertifikası oluşturur ve yeniler.
  - Container adı: `nginx-letsencrypt`
  - Environment:
    - `NGINX_PROXY_CONTAINER=nginx-proxy`
  - Depends on: `nginx-proxy`
  - Network: `proxy_net`

### 🌐 Network

- Tüm web container’ları ve proxy container’ları `proxy_net` ağı üzerinden iletişim kurar.
- `proxy_net` dış bir network olarak tanımlıdır.

### 💡 Kullanım Notları

- Yeni bir web sitesi eklemek için ilgili container’i başlatıp **VIRTUAL_HOST** ve **LETSENCRYPT_HOST** environment değişkenlerini ayarlamanız yeterlidir.
- Nginx proxy ve Let's Encrypt otomatik olarak çalışır ve SSL sertifikası sağlar.
- Tüm sertifikalar ve virtual host konfigürasyonları `./certs` ve `./vhost.d` dizinlerinde saklanır.

---

### 📝 Örnek docker-compose Dosyası

```yaml
version: '3.8'

services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    container_name: nginx-proxy
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - ./certs:/etc/nginx/certs:rw
      - ./vhost.d:/etc/nginx/vhost.d
      - ./html:/usr/share/nginx/html
    networks:
      - proxy_net

  letsencrypt:
    image: nginxproxy/acme-companion
    container_name: nginx-letsencrypt
    restart: always
    environment:
      - NGINX_PROXY_CONTAINER=nginx-proxy
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./certs:/etc/nginx/certs:rw
      - ./vhost.d:/etc/nginx/vhost.d
      - ./html:/usr/share/nginx/html
    depends_on:
      - nginx-proxy
    networks:
      - proxy_net

networks:
  proxy_net:
    external: true
```
