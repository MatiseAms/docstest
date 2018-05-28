## Production.lftp file
This is a basic example of our ftp deploy file, check with the [generator](https://github.com/MatiseAms/generator-matise) if this is the latest version.

Variables are: 
- ```[username]```
- ```[password]```
- ```[host]```
- ```[path]```

```bash
set ftp:list-options -a
set cmd:fail-exit true
set ftp:ssl-allow off
set ssl:verify-certificate no
open -u [username],[password] [host]:/[path]
lcd .
mirror -R -n -v --parallel=4 \
    --exclude content/uploads/ \
    --exclude content/plugins/ \
    --exclude wordpress
quit
```

## Pulldatabase.sh file
This is a basic example of our database clone script, check with the [generator](https://github.com/MatiseAms/generator-matise) if this is the latest version.

Variables are: 
- ```[stagingdbname]```
- ```[stagingprojectfolder]```

```bash
ssh staging@s01.matise.nl -- 'mysqldump [stagingdbname] > backup.sql'
echo 'dumped database'
rsync -v -e ssh staging@s01.matise.nl:~/backup.sql ./
echo 'downloaded database'
ssh staging@s01.matise.nl -- 'rm backup.sql'
echo 'removed backup from server'
vagrant ssh  -- 'mysql --user=root --password=root -Nse "show tables" scotchbox | while read table; do mysql --user=root --password=root -e "drop table $table" scotchbox; done'
echo 'dropped all local tables'
vagrant ssh -- 'mysql --user=root --password=root scotchbox < /var/www/backup.sql'
echo 'restored downloaded database'
vagrant ssh -- 'rm /var/www/backup.sql'
echo 'removed local database file'
rsync -avz -e ssh staging@s01.matise.nl:/home/staging/mati.se/httpdocs/[stagingprojectfolder]/content/uploads/ ./public/content/uploads
echo 'Uploaded content from server is in sync!'
```


## Vagrant file
This is a basic example of our vagrant files, check with the [generator](https://github.com/MatiseAms/generator-matise) if this is the latest version.

Variables are: 
- ```<%= appName %>```

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "scotch/box-pro"
    config.vm.hostname = "<%= appName %>.matise"
    config.vm.network "forwarded_port", guest: 80, host: 8080
    config.vm.network "private_network", ip: "192.168.33.10"
    config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

end
```
