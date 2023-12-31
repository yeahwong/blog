# 香橙派3B：丐版HomeLab之良品


## 開始

互聯網資本寡頭所提供的各類雲服務，從社交媒體再到同步服務，人們愈發不再信任。「從來就沒有什麼救世主，也不靠神仙皇帝」，那些沒有受虐傾向的人們，那些沒有露體癖的人們，那些不甘被資本與權力宰制的人們，紛紛拿起開源武器，開始了數據的自我賦權，啟動了本地優先之策略。

歲月催人老，泰西2023年已然離去。9月底，本呆採購了一台香橙派作為HomeLab，它在我的出租屋已成功運行了兩個月。現給大家做一個報告，亦作為學習的記錄。

相較各類昂貴和功耗高的x86服務器， 功耗降低的arm架構的單板機是HomeLab之廉價選擇。樹莓派，大名鼎鼎，但它已成為理財產品，為大家所詬病，當然不在我的選擇範圍。思來想去，左等右等，我選購了國產廠商推出的香橙派3B的最高配8G版，它採用RK3566，性能足夠使用。後面，我又採購了電源適配器、SSD硬碟、U盤、散熱片、外殼等。

| 物件 | 採購平台 | 價格 |
|---|---|---|
| Orange Pi 3B 8G版 | 淘寶OrangePi官方店 | 305.72元 |
| 電源與電源線 | 淘寶某店鋪 | 16.32元 |
| 金屬外殼 | 淘寶某店鋪 | 62元 |
| 128G 2230 SSD硬碟 （二手） | 拼多多某店鋪 | 36元 |
| 硬碟散熱銅片 | 淘寶某店鋪 | 11.64元 |
| 128G 金屬外殼U盤 | 京東京造自營店 | 45.9元 |
| U盤散熱銅柱 | 淘寶某店鋪 | 33.16元 |

以上，列舉了HomeLab實際運行的配件，並沒有包括螺絲刀、鉗子、串口線、採集卡、HDMI線、美工刀等，還有一個被折騰壞的壓克力外殼，好在很多器物都是可以重複再利用的。顯然，與各類二手礦難盒子相比，這並不便宜，但好在接口豐富，我看中了它可以直接插2230SSD硬碟，而且在拼多多購買的SSD硬碟好像也沒有通電多少次，感覺超值。

{{< figure src="/images/orangepi-3b-as-homelab/2.png" >}}

於此HomeLab之中，本地安裝[armbian](https://www.armbian.com/)系統，部署了docker，運行了[gotosocial](https://gotosocial.org/)、[IPFS](https://ipfs.tech/)、clash、[Cloudflare Tunnel](https://www.cloudflare.com/products/tunnel/)、[青龍面板](https://github.com/whyour/qinglong)等容器，還在持續探索之中。

組裝完畢，我們開始吧。

## 安裝操作系統

根據的OrangePI[官方用戶手冊](http://www.orangepi.cn/html/hardWare/computerAndMicrocontrollers/service-and-support/Orange-Pi-3B.html)的指引，要將系統燒錄到NVMeSSD硬碟，首先需將bootloader燒錄至SPIFlash，才能確保系統從NVMeSSD硬碟啟動。那麼整個流程就簡單明確了，先將系統燒錄至SD卡，通電開機後；再將bootloader和系統分別燒錄至SPIFlash和NVMeSSD硬碟；最後，關機拔掉SD卡，再通電開機即可。

這裡，我採用armbian提供3B的社區維護版系統，SD卡的燒錄工具為[balenaEtcher](https://github.com/balena-io/etcher)。下載好系統，安裝好燒錄工具即可開始作業。

{{< figure src="/images/orangepi-3b-as-homelab/3.png" >}}

SD卡連接至電腦後，打開balenaEtcher，在交互介面中選擇下載好鏡像文件和SD卡。

{{< figure src="/images/orangepi-3b-as-homelab/4.png" >}}

點擊Flash按鈕，等待燒錄完成。將SD從電腦彈出後，插入香橙派的SD卡卡槽，通電啟動。在路由器的後台，找到香橙派的本地IP。有了IP後，打開終端。

```
ssh root@your_ip_address
```

armbian的root默認密碼為1234，根據提示選擇終端類型與語言、地區等，修改默認root密碼以及創建新用戶。完成初始化設置之後，在終端鍵入命令，進入armbian的設置介面。

```
sudo armbian-config
```

{{< figure src="/images/orangepi-3b-as-homelab/5.png" >}}

選擇`System`選項。

{{< figure src="/images/orangepi-3b-as-homelab/6.png" >}}

選擇`Install`選項。

{{< figure src="/images/orangepi-3b-as-homelab/7.png" >}}

選擇`4`選項。

{{< figure src="/images/orangepi-3b-as-homelab/8.png" >}}

因為我們沒有插入U盤或者其它設置，故選擇選擇`1`選項。

{{< figure src="/images/orangepi-3b-as-homelab/9.png" >}}

選擇`Yes`選項，將NVMeSSD硬碟清除數據格式化。

{{< figure src="/images/orangepi-3b-as-homelab/10.png" >}}

待進度格式化進度跑完後，選擇`1`選項。**注意**：香橙派的bootloader不支援btrfs格式的系統從NVMeSSD硬碟啟動，只能選擇ext4格式。

{{< figure src="/images/orangepi-3b-as-homelab/11.png" >}}

耐心等待進度跑完，期間香橙派的藍色信號燈會快速閃爍。

{{< figure src="/images/orangepi-3b-as-homelab/12.png" >}}

NVMeSSD硬碟的寫出完成後，選擇`7`選項，將bootloader燒錄至SPIFlash。

{{< figure src="/images/orangepi-3b-as-homelab/13.png" >}}

選擇`Yes`選項，開始燒錄。

{{< figure src="/images/orangepi-3b-as-homelab/14.png" >}}

選擇`Power off`選項，關機。等到電源指示燈熄滅後，拔出SD卡，再次通電。這次，armbian將會從NVMeSSD硬碟啟動，啟動期間藍色指示燈會快速閃爍。待啟動完成，進入路由器後台，找到新的IP。

## 一些基本設置

### 修改軟件源

```
ssh root@your_new_ip_address
```

受限於國朝網路，需要將軟件源切換至[北平清華大學的鏡像](https://mirrors.tuna.tsinghua.edu.cn/)，才能獲得良好的速度。

```
sudo nano /etc/apt/sources.list.d/armbian.list
```

將`http://apt.armbian.com`替換為`https://mirrors.tuna.tsinghua.edu.cn/armbian`。

編輯sources.list。

```
nano /etc/apt/sources.list
```

先講原有內容註釋掉，然後至北平清華大學的[Debian軟件源](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)，選擇相應的版本，粘貼至sources.list後，保存退出。

```
sudo apt update && sudo apt upgrade
```

運行以上命令，更新軟體列表，根據提示鍵入`y`，更新軟體。

### SSH設置

為安全起見，這裡我們修改SSH的默認端口，取消root登錄，並取消密碼登錄，改為密鑰登錄。

```
mkdir -p ~/.ssh
```

運行以上命令，創建文件夾`.ssh`。

```
touch ~/.ssh/authorized_keys
nano ~/.ssh/authorized_keys
```

運行以上命令，創建authorized_keys文件，並將公鑰粘貼至此。

```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

運行以上命令，修改文件夾及文件的權限。

```
sudo nano /etc/ssh/sshd_config
```

運行以上命令，編輯SSH的配置文件。

```
port your_ssh_port_number #SSH端口號
PermitRootLogin no #拒絕root登錄
PubkeyAuthentication yes #允許密鑰登錄
PasswordAuthentication no #拒絕密鑰登錄
```

修改以上內容，保存並退出。

```
sudo systemctl restart ssh
```

運行以上命令，重啟SSH服務後，就可以使用密鑰登錄了。

### 開啟smaba服務

為方便在電腦中管理香橙派的文件，我們需部署smaba服務，以獲得遠端管理香橙派的文件有管理本地文件的同等體驗。

```
sudo apt install samba
```

運行以上命令，安裝samba軟體。

```
sudo smbpasswd -a $USER
```

運行以上命令，為當前用戶創建samba登錄密碼。

```
sudo nano /etc/samba/smb.conf
```

運行以上命令，編輯samba的配置文件。

```
[global]
   workgroup = WORKGROUP
   server string = orange pi 3b
   security = user
   map to guest = Bad User
   encrypt passwords = yes
   passdb backend = tdbsam

   min protocol = SMB2
   client min protocol = SMB2
   client max protocol = SMB3

   # local master = no
   # preferred master = no

   log file = /var/log/samba/log.%m
   max log size = 50


[orangepi]
   comment = User Files
   path = /home/your_username
   browseable = yes
   writeable = yes
   guest ok = no
   create mask = 0664
   directory mask = 0775
```

修改`your_username"為正確的用戶名，以上內容替換至配置文件中。

```
sudo systemctl start smbd
sudo systemctl enable smbd
```

運行以上命令，啟動samba，並設置為開機啟動。

### 防火牆

```
sudo apt install ufw
```

運行以上命令，安裝防火牆軟體ufw。

```
sudo ufw allow 139/tcp
sudo ufw allow 445/tcp
#以上，允許samba服務通過防火牆
sudo ufw allow your_ssh_port_number/tcp
#以上，允許SSH服務通過防火牆
```

運行以上命令，將SSH和samba允許通過防火牆。

```
sudo ufw enable
sudo systemctl enable ufw
```

運行以上命令，啟動ufw防火牆，並設置為開機啟動。

### 必要之設定

#### 網路的吞吐參數

```
net.core.rmem_max=2500000
net.core.wmem_max=2500000
```

運行以上命令，修改網路吞吐參數，以便IPFS更好地運行。

```
sudo nano /etc/sysctl.conf
```

運行以上命令，講前述修改網路參數的命令添加至sysctl.conf，以便重啟後繼續生效。

#### 網卡的混合模式

運行`ip link`，找到設備的乙太網接口名稱。

```
sudo ip link set your_eth_name promisc on
```

運行以上命令，開啟網卡混合模式。

```
sudo nano /etc/rc.local
```

運行以上命令，編輯`rc.local`文件。

```
sudo ip link set your_eth_name promisc on
```

在`exit 0`行之前，添加以上內容，保存並關閉文件。

#### 刪除無線網卡

```
sudo systemctl disable wpa_supplicant
```

運行以上命令，禁用WiFi，節約用電和資源佔用。

## 數據備份

數據容災是必修課，良好的數據備份體驗是許多人使用各類雲服務的理由，那打造一個好的HomeLab，就需確保數據有容災能力。這裡，我們使用rsync軟體，採用阮一峰的提供的[增量備份腳本](https://www.ruanyifeng.com/blog/2020/08/rsync.html)，將數據備份至U盤。

考慮U盤的發熱問題，這裡建議選購金屬外殼的U盤，並加裝散熱銅柱，可較好地散熱，以增加U盤的使用時間。

### 掛載U盤

首先，將U盤插入香橙派的USB3.0接口，該接口顏色為藍色。

運行`lsblk`。

找到U盤的名字。

```
sudo mkfs.btrfs /dev/sda1
```

運行以上命令，將U盤格式化btrfs文件系統。

```
sudo mkdir /mnt/backup
```

運行以上命令，創建一個目錄，以便掛載U盤。

```
sudo mount /dev/sda1 /mnt/backups
```

運行以上命令，挂载U盤至新創建的目錄。

```
sudo blkid /dev/sdb1
```

運行以上命令，獲取`sdb1`的UUID。

```
sudo nano /etc/fstab
```

編輯`/etc/fstab`文件。

```
UUID=your_UUID /mnt/myusb btrfs defaults 0 0
```

在`/etc/fstab`文件中添加以上內容為行。

### 自動備份腳本

```
sudo apt install rsync
```

運行以上命令，安裝rsync至armbian。

```
nano ~/script/backup_script_usb.sh
```

運行以上命令，創建腳本文件。

```
#!/bin/bash

# A script to perform incremental backups using rsync and retain only the last 28 backups

set -o errexit
set -o nounset
set -o pipefail

readonly SOURCE_DIR="/home/your_username/"
readonly BACKUP_DIR="/mnt/backup"
readonly DATETIME="$(date '+%Y-%m-%d_%H:%M:%S')"
readonly BACKUP_PATH="${BACKUP_DIR}/${DATETIME}"
readonly LATEST_LINK="${BACKUP_DIR}/latest"

mkdir -p "${BACKUP_DIR}"

# Perform the backup
rsync -av --delete --exclude=".cache" "${SOURCE_DIR}/" --link-dest "${LATEST_LINK}" "${BACKUP_PATH}"

# Remove old backups, keeping only the last 28
find "${BACKUP_DIR}" -mindepth 1 -maxdepth 1 -type d | sort | head -n -28 | xargs rm -rf

# Remove the existing "latest" link and create a new one
rm -f "${LATEST_LINK}"
ln -s "${BACKUP_PATH}" "${LATEST_LINK}"
```

講以上內容粘貼至腳本文件。

```
chmod +x ~/script/backup_script_usb.sh
```

運行以上命令，為腳本賦予執行權限。

```
sudo crontab -e
```

運行以上命令，編輯crontab。

```
30 */6 * * * ~/script/backup_script_usb.sh  >> ~/script/log/backup_script_usb.log 2>&1
```

將以上內容寫入crontab文件為行。

通過掛載U盤，並通過自動備份腳本，我們可以實現每日備份4次的增量備份，並將備份數據保持一週的時間。之後，本呆會將數據也備份至Nas，以增加容災能力。

## 結束

{{< figure src="/images/orangepi-3b-as-homelab/15.png" >}}

以上，我們通過初步設置將丐版HomeLab配置完成。要實現本地優先之策略，我們需要將諸多服務部署至HomeLab，以造抵抗之器。後續，本呆會發文介紹目前所使用一些docker容器。

丐版HomeLab，夠用就好。這顆28nm的RK3566性能足以覆蓋基本的需求，倘若追求更強更好的配置，恐怕會陷入至消費主義的陷阱。也許每一互聯網移民都應有一個屬於自己的HomeLab，這也是初民之要義。反動學人福山都在用單板機，妳還在等什麼？
