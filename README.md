# Some SSL Commands
Some SSL Commands OS : Ubuntu 22.04

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

#### MySQL Kullanıcı Oluşturma ve Yetkilerini Ayarlama
~~~ ssh
mysql -u root -p (Root ile mysqle bağlanıyoruz)
CREATE USER 'orhancelik'@'%' IDENTIFIED BY '12345'; (Kullanıcıyı oluşturuyoruz)
SELECT * FROM mysql.user; (Kullanıcıları kontrol ediyoruz)
(sadece okuma ve yazma yetkisi) => GRANT SELECT, INSERT ON [veritabanı adı yada tüm veritabanı (*)].* TO 'orhancelik'@'%';
(tüm yetkiler) => GRANT ALL ON *.* TO 'orhancelik'@'%' WITH GRANT OPTION; (Okuma ve yazma yetkisi veriyoruz)
FLUSH PRIVILEGES; (değişiklikleri hemen uyguluyoruz)
\q (Çıkış yapıyoruz)
~~~
