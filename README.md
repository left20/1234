@@ -1,27 +1,124 @@
# Samba 伺服器架設

<!-- vim-markdown-toc GFM -->

* [SMB 協定簡介](#smb-協定簡介)
* [Samba 簡介](#samba-簡介)
* [安裝samba](#安裝samba)
* [建立共享目錄](#建立共享目錄)
* [修改samba配置檔案](#修改samba配置檔案)
* [設定登入密碼](#設定登入密碼)
* [啟動samba伺服器](#啟動samba伺服器)
* [測試是否共享成功](#測試是否共享成功)
  - [開啟windows檔案管理器，輸入\\ip地址或主機名\share](#開啟windows檔案管理器，輸入\\ip地址或主機名\share)
  
* [參考資料](#參考資料)

<!-- vim-markdown-toc -->

## SMB 協定簡介
SMB (Server Message Block) 通訊協定最早是在 1980 年代由 IBM 所發展出來，這個通訊協定可以讓不同的電腦共享檔案、印表機、及其它裝置。SMB 最早是運作在 NetBIOS 的網路協上，一開始 IBM 設計了 NetBIOS 只是為了讓網路中少數的電腦可以彼此互相溝通。所以 NetBIOS 的功能比較陽春，在使用上有一些限制。例如，只能使用在區域網路中、跨不過路由器。後來又出現了 NetBEUI (NetBIOS Extend User Interface)，也是 IBM 針對 NetBIOS 的改良版。後來又有 NetBIOS over TCP/IP，使得 SMB 協定也可以跑在 TCP/IP 上，並可以經由網際網路存取。在使用 NetBIOS 在存取遠端電腦時，我們可以使用電腦名稱如「\\alex-pc」來存取，而 TCP/IP 只能使用「\\192.168.0.1」這種方式來存取。所以 NetBIOS 也不是一無是處，它提供了比較簡單而易懂的方式。

Microsoft 在 1996 年為了市場考量，將 SMB 改名為 CIFS (Common Internet File System)，名稱看起來比較容易了解，顧名思義就是可以在網際網路上使用的檔案系統。所以我們其實不應該稱呼網路上的芳鄰為「網路芳鄰」，而是 SMB/CIFS 通訊協定，就好像我們稱呼 HTTP、FTP 一樣。在 Windows 的世界裡，人們可以使用 SMB/CIFS 來共享檔案。而在 UNIX 的世界裡使用的是 NFS (Network File System)；Mac 的世界中則是使用 AppleTalk。我們可以看到不同的作業系統環境中，都有自己的一套方法，不過我們還是可以經 由安裝軟體來達成不同平台共享檔案的功能。

## Samba 簡介

安裝了 Samba 之後，網路上的所有電腦都可以使用這台伺服器共享檔案，而不必另外再開一台 Windows 機器。編輯網頁、寫程式時，會使用 Samba 將我的網頁資料、程式直接分享出來，再由 Windows 上的文書編輯軟體來進行編輯。存檔後，不必再使用 FTP 上傳檔案，而是可以直接使用瀏覽器看到編輯的結果。

Samba 的功能可不只有檔案分享，它還可以讓我們使用 UNIX 加入 Windows 的 Domain、ADS，以使用 Windows 的 Domain 使用者權限控管。

## 安裝samba

- 安裝

```shell
sudo apt-get install samba
```

- 建立共享目錄     

```
// 例如：在使用者gzd的主目錄下新建share資料夾為共享目錄
mkdir /home/gzd/smbshare
// 由於Windows下的資料夾需可讀可寫可執行，需更改許可權為777
sudo chmod 777 /home/gzd/smbshare
```

![ftp-port](img/ftp-port.png) 

- 確認服務狀態

```
sudo systemctl status vsftpd.service
```

![ftp-status](img/ftp-status.png) 

## 修改samba配置檔案

```shell
sudo vim /etc/samba/smb.conf
[share]
path = /home/gzd/smbshare
public = yes
writable = yes
valid users = gzd
create mask = 0644
force create mode = 0644
directory mask = 0755
force directory mode = 0755
available = yes
```

## 設定登入密碼

```
sudo touch /etc/samba/smbpasswd
sudo smbpasswd -a gzd
```


### 啟動samba伺服器

```shell
sudo /etc/init.d/samba restart
```

### 更改使用者

```shell
sudo chown ftp:ftp /var/ftp/
```

##測試是否共享成功

```shell
sudo apt-get install smbclient 
smbclient -L //localhost/share
```

### 在windows上測試

```shell
開啟windows檔案管理器，輸入\\ip地址或主機名\share
Linux的ip地址可通過ifconfig檢視
選擇記住憑據，下次輸入地址後無需登入
第一次開啟可能需要幾秒時間，耐心一點
```


## 參考資料

[Wiki FTP](https://zh.wikipedia.org/zh-tw/%E6%96%87%E4%BB%B6%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE) 

[Ubuntu FTP server 架設](https://www.alvinchen.club/2019/12/11/ubuntu-ftp-server-%E6%9E%B6%E8%A8%AD/) 

[ubuntu 安裝 ftp 與設定權限](https://oranwind.org/-ftp-ubuntu-an-zhuang-ftp-yu-she-ding-apache-du-qu-quan-xian/) 

[使用Ubuntu Server架設FTP伺服器](https://magiclen.org/ubuntu-server-vsftpd/) 
