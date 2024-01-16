# 網路受限下部署Fediverse實例GoToSocial


## 緣起

帝國的旗幟隨風飄揚，新皇冉冉升起。盆地到雪山，稻田到工廠，白天到黑夜，祂的身影籠罩整片大地。城牆內外，禮樂崩壞，舊有秩序轟然倒塌，新人尚未現身。人們畏懼開口，每個人頭上都戴著枷鎖，自我放逐與驅逐他人成為日常生活。

言說，在何處言說，業已成為現代臣民的必修課。不消說封閉的社交網路的書報審查，長篇累牘的賣身契自不待言，有誰會甘願淪為賽博農奴，不知疲倦地為科技資本寡頭生產數據。那麼，我們何以為家？從馬一龍的肆意妄為，再到扎克伯格的生意經，互聯互通的聯邦社交網路似乎成為無奈之選。

在聯邦宇宙之中，一旦選擇加入一個實例，即意味著簽下契約，我們的帳戶隨時面臨管理者之鎚，與此同時，還要時刻擔心站點倒閉造成的數據丟失。相對應地，倘如對實例進行自託管，那只需追尋內心，同時承擔相應的風險。

於城牆之內，網路被阻擋是對臣民的恩澤。誠如口罩可以成為主權者的強制，但也讓人們有了戴口罩規避監視器的權利，網路受限使得臣民被迫武裝在賽博空間。有了武器，借助 [Cloudflare Tunnel](https://www.cloudflare.com/products/tunnel/)，我們能夠輕鬆地在受限地區的家庭網路中部署 [GoToSocial](https://gotosocial.org/)，在聯邦宇宙中互通交流。

GoToSocial 是一個使用 Golang 編寫的 ActivityPub 社交網絡伺服器。至於為什麼選擇 GoToSocial？因為它需要的資源足夠少，單板機足以運行，且開發者較為活躍。我的實例使用 [docker](https://www.docker.com/) 部署在香橙派3B，那我們開始吧。

## 部署

{{< figure src="/images/how-to-use-gotosocial/2.png" >}}

在網路受限的情況下，例如無法直接訪問 [Mastodon](https://joinmastodon.org/) 的官方實例 [mastodon.social](https://mastodon.social/)，我們需要依賴 Clash 作為跳板，以建立到聯邦宇宙的連接。同時，由於網路受限，無法獲得公網 IP，因此我們必須借助 Cloudflare Tunnel，將 GoToSocial 實例暴露在公共網絡上，以供訪問。

### 安裝 docker，部署 Clash

由此可知，部署 clash 是首要步驟。要部署 clash 容器，首先要在系統上安裝 docker。

```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh --mirror Aliyun
```

使用以上命令，安裝 docker，並指定鏡像源為 Aliyun。

```
docker network create -d macvlan \
  --subnet=your_subnet/24 \
  --gateway=your_router_ip \
  -o parent=your_eth_name \
  macvlan
```

使用以上命令，在docker中創建一個 macvlan 网络，clash 容器連結到這個網路後，使用與主路由相同的網段。其中，parent 為設備的以太網網卡名稱。設置路由器 ip 為 192.168.1.1，以上的 subnet 為 192.168.1.0/24， gateway 為  192.168.1.1。 

```
docker run -d \
  --name clash \
  -e USER_ID=1000 \
  -e GROUP_ID=1000 \
  -v ~/docker/clash/config/proxy.yaml:/root/.config/clash/config.yaml \
  -v ~/docker/clash/ui:/ui \
  --restart=always \
  --network=macvlan \
  --ip=your_clash_ip \
  --privileged \
  --device=/dev/net/tun \
  dreamacro/clash-premium:latest
```

使用以上命令，部署 clash 容器。其中詳細配置，請參照文章「[Docker Clash 旁路由网关模式透明代理](https://haoyu.love/blog1412.html)」，這裡不再贅述。

### 部署 GoToSocial

完成 clash 的部署後，下載 GoToSocial 的配置文件 [config.yaml](https://github.com/superseriousbusiness/gotosocial/blob/main/example/config.yaml)，修改以下內容。

```
# Both allow-ips and block-ips default to an empty array.
  allow-ips: ["your_device_ip/32"]
  block-ips: []
```

完成修改後，將 config.yaml 拷貝至 `~/docker/gotosocial/`。其中 `your_device_ip` 為妳的伺服器在內網的IP地址。

```
docker run -d \
  --name gotosocial \
  -p your_gotosocial_port:8080 \
  -e GTS_HOST=your_gts_domain_name \
  -e GTS_DB_TYPE=sqlite \
  -e GTS_DB_ADDRESS=/gotosocial/storage/sqlite.db \
  -e GTS_LETSENCRYPT_ENABLED=false \
  -e TZ=Asia/Taipei \
  -e USER_ID=1000 \
  -e GROUP_ID=1000 \
  -e HTTP_PROXY=socks5://your_clash_ip:your_clash_port \
  -e HTTPS_PROXY=socks5://your_clash_ip:your_clash_port \
  -v ~/docker/gotosocial/data:/gotosocial/storage \
  -v ~/docker/gotosocial/config.yaml:/gotosocial/config.yaml \
  --restart=always \
  superseriousbusiness/gotosocial:latest \
  /gotosocial --config-path ./config.yaml server start
```

運行以上命令，部署 GoToSocial。其中 `GTS_HOST` 為 GoToSocial 的域名，這裡需要將域名添加至 Cloudflare，並域名服務商處將 DNS 修改為 Cloudflare 的 DNS。接下來，我們只需要將 GoToSocial 的本地端口連結至 Cloudflare Tunnel，就萬事大吉了。

### 部署 Cloudflare Tunnel

要使用 Cloudflare Tunnel 服務，首先要登錄 Cloudflare，註冊 [Zero Trust](https://www.cloudflare.com/zero-trust/)，選擇免費套餐即可。

{{< figure src="/images/how-to-use-gotosocial/3.png" >}}

完成註冊，進入 Zero Trust 後台，選擇 Tunnels 菜單，Create a tunnel （新建一個隧道）。

{{< figure src="/images/how-to-use-gotosocial/4.png" >}}

完成創建後，進入以上頁面，選擇 Docker，其中 `--token` 之後的字串為 `your_token`，請勿將該字串洩漏給任何第三方，以免相關服務被劫持。

```
docker run -d \
  --name cloudflare_tunnel \
  -e USER_ID=1000 \
  -e GROUP_ID=1000 \
  -e HTTP_PROXY=socks5://your_clash_ip:your_clash_port \
  -e HTTPS_PROXY=socks5://your_clash_ip:your_clash_port \
  --restart=always \
  cloudflare/cloudflared:latest-arm64 tunnel --no-autoupdate \
  run --token your_token
```

獲得 `your_token` 之後，使用以上命令部署 Cloudflare Tunnel。

{{< figure src="/images/how-to-use-gotosocial/5.png" >}}

回到 Cloudflare Tunnel 的後台，返回至隧道列表，進入至指定隧道的配置頁面。選擇公開服務，點擊增加一個新的公網域名。

{{< figure src="/images/how-to-use-gotosocial/6.png" >}}

於 Subdomain 初寫入想要的二級域名，亦可不寫；於 Domain 初選擇之前添加的域名；於 Type 初選擇 HTTP；於 URL 寫入 `your_device_ip:your_gotosocial_port`。

至此，我們部署已完成，若如無意外，我們的 GoToSocial 已進入聯邦宇宙。

## 用戶設置與使用

### 設置

日前，GoToSocial 並無完善的用戶管理頁面，需要使用命令行來管理用戶。

```
docker exec -it gotosocial \
  /gotosocial/gotosocial admin account create \
  --username your_username \
  --email your_email \
  --password 'your_password'
```

使用以上命令，創建一個用戶。完成用戶創建後，可以登錄 `your_domain_name/admin`，進行一些必要之用戶設置，比如暱稱、頭像等。

```
docker exec -it your_container_name \
  /gotosocial/gotosocial admin account promote \
  --username your_username
```

使用以上命令，將一個用戶提升至管理員。使用管理員帳戶登錄 `your_domain_name/admin`，可以進行一些全站設置。

```
docker exec -it your_container_name \
  /gotosocial/gotosocial admin account demote \
  --username your_username
```

使用以上命令，將一個管理員降級至普通用戶。

```
docker exec -it your_container_name \
  /gotosocial/gotosocial admin account password \
  --username your_username \
  --password your_new_password

使用以上命令，修改某個用戶的密碼。
```

### 使用

日前，GoToSocial 亦無一個網頁來進行內容發布與瀏覽，但它可以兼容 Mastodon 的一些客戶端，可訪問 [Mastodon App](https://joinmastodon.org/apps) 列表查看更多。

我的聯邦宇宙帳戶為 `@yeahwong@hi.sogo.la`，歡迎關注。

## 參考資料

- [GoToSocial Documentation](https://docs.gotosocial.org/en/latest/)

- [搭建自己的节点之 Gotosocial 篇](https://tourcoder.com/how-to-use-gotosocial/)
