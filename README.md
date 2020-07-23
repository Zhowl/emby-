# emby
#VPS搭建EMBY和挂载Google Drive Part1
1，安装emby

wget https://github.com/MediaBrowser/Emby.Releases/releases/download/4.4.2.0/emby-server-deb_4.4.2.0_amd64.deb

dpkg -i emby-server-deb_4.4.2.0_amd64.deb

xxx.xxx.xxx.xxx:8096

如果你还是打不开那可能是因为端口未开放的缘故 Ubuntu系统下打开端口

iptables -F

#VPS搭建EMBY和挂载Google Drive Part2

1、安装rclone

curl https://rclone.org/install.sh | sudo bash

2、配置

rclone config

o remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n

name emby 随意取 但是后面的代码也会相应改变

Choose a number from below, or type in your own value
选谷歌 （13） 可能会因为版本的不同而改变 注意选择 Google drive

client_id> 直接回车

client_secret> 直接回车

Choose a number from below, or type in your own value
scope> 1 选1 有最大的使用权限。

ID of the root folder
Leave blank normally.

Fill in to access "Computers" folders (see docs), or for rclone to use
a non root folder as its starting point.

Note that if this is blank, the first time rclone runs it will fill it
in with the ID of the root folder.

Enter a string value. Press Enter for the default ("").
root_folder_id> 空，直接回车。空是跟路径如果想用别的根路径

Service Account Credentials JSON file path
Leave blank normally.
Needed only if you want use SA instead of interactive login.
Enter a string value. Press Enter for the default ("").
service_account_file> 直接回车

Edit advanced config? (y/n)
y) Yes
n) No (default)
y/n> n 不用别的高级配置

Use auto config?

Say Y if not sure
Say N if you are working on a remote or headless machine
y) Yes (default)
n) No
y/n> n 因为我们是vps操作，不能auto config
Please go to the following link: https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Log in and authorize rclone for access
Enter verification code>贴入你获取到的key

Configure this as a team drive?
y) Yes
n) No (default)
y/n> y

Choose a number from below, or type in your own value
1 / 影音云端盘
\ "0AGfwXXXXXXXXXXXX"
2 / homenet6精英nas盘
\ "0AXXXXXXXXXXXXXXX"
Enter a Team Drive ID>

y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q

3.挂载

mkdir -p /home/gdrive

/usr/bin/rclone mount emby: /home/gdrive \
 --umask 0000 \
 --default-permissions \
 --allow-non-empty \
 --allow-other \
 --buffer-size 32M \
 --dir-cache-time 12h \
 --vfs-read-chunk-size 64M \
 --vfs-read-chunk-size-limit 1G &
——————————

4、查看挂载

df -h
5、自动挂载

cat > /etc/systemd/system/rclone.service <<EOF
[Unit]
Description=Rclone
AssertPathIsDirectory=LocalFolder
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount emby: /home/gdrive \
 --umask 0000 \
 --default-permissions \
 --allow-non-empty \
 --allow-other \
 --buffer-size 32M \
 --dir-cache-time 12h \
 --vfs-read-chunk-size 64M \
 --vfs-read-chunk-size-limit 1G
ExecStop=/bin/fusermount -u LocalFolder
Restart=on-abort
User=root

[Install]
WantedBy=default.target
EOF
6、设置启动

systemctl start rclone

7、开启启动

systemctl enable rclone

8、安装bbrplus 这里和之前搭建SSR之类使用BBR加速一样 不赘述

wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh

9、虚拟内存

wget https://www.moerats.com/usr/shell/swap.sh && bash swap.sh


#报错解决
挂载报错：2020/05/20 14:53:12 Fatal error: failed to mount FUSE fs: fusermount: exec: "fusermount": executable file not found in $PATH
解决办法：apt-get install fuse -y

解压报错
apt update && apt install curl unzip -y

#VPS搭建EMBY和挂载Google Drive Part3 挂载第二个盘 

1、配置

rclone config

o remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n

name emby1 随意取 但是后面的代码也会相应改变

Choose a number from below, or type in your own value
选谷歌 （13） 可能会因为版本的不同而改变 注意选择 Google drive

client_id> 直接回车

client_secret> 直接回车

Choose a number from below, or type in your own value
scope> 1 选1 有最大的使用权限。

ID of the root folder
Leave blank normally.

Fill in to access "Computers" folders (see docs), or for rclone to use
a non root folder as its starting point.

Note that if this is blank, the first time rclone runs it will fill it
in with the ID of the root folder.

Enter a string value. Press Enter for the default ("").
root_folder_id> 空，直接回车。空是跟路径如果想用别的根路径

Service Account Credentials JSON file path
Leave blank normally.
Needed only if you want use SA instead of interactive login.
Enter a string value. Press Enter for the default ("").
service_account_file> 直接回车

Edit advanced config? (y/n)
y) Yes
n) No (default)
y/n> n 不用别的高级配置

Use auto config?

Say Y if not sure
Say N if you are working on a remote or headless machine
y) Yes (default)
n) No
y/n> n 因为我们是vps操作，不能auto config
Please go to the following link: https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
Log in and authorize rclone for access
Enter verification code>贴入你获取到的key

Configure this as a team drive?
y) Yes
n) No (default)
y/n> y

Choose a number from below, or type in your own value
1 / 影音云端盘
\ "0AGfwXXXXXXXXXXXX"
2 / homenet6精英nas盘
\ "0AXXXXXXXXXXXXXXX"
Enter a Team Drive ID>

y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q

2.挂载

mkdir -p /home/gdrive1

/usr/bin/rclone mount emby1: /home/gdrive1 \
 --umask 0000 \
 --default-permissions \
 --allow-non-empty \
 --allow-other \
 --buffer-size 32M \
 --dir-cache-time 12h \
 --vfs-read-chunk-size 64M \
 --vfs-read-chunk-size-limit 1G &
——————————

3、查看挂载

df -h
4、自动挂载

cat > /etc/systemd/system/rclone1.service <<EOF
[Unit]
Description=Rclone
AssertPathIsDirectory=LocalFolder
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount emby1: /home/gdrive1 \
 --umask 0000 \
 --default-permissions \
 --allow-non-empty \
 --allow-other \
 --buffer-size 32M \
 --dir-cache-time 12h \
 --vfs-read-chunk-size 64M \
 --vfs-read-chunk-size-limit 1G
ExecStop=/bin/fusermount -u LocalFolder
Restart=on-abort
User=root

[Install]
WantedBy=default.target
EOF
systemctl start rclone1
systemctl enable rclone1

