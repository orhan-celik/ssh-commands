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
