# apache-virtual-host
Some SSL Commands OS : Ubuntu 22.04

#### - 2 sunucu arasında ssh dosya taşıma
~~~ ssh
sudo scp -r -P22 /var/www/html/* [target_user]@[target_server_ip]:/var/www/html/tasima_yedekleri/
~~~

#### - 2 sunucu arasında ssh dosya taşıma
~~~ ssh
find /var/www/html/[search_folder_path]/ -name "*.[search_extension]" -exec grep -l "[search_text]" {} \;
~~~
