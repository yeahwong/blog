# 我的博客方案：靜態網站與 IPFS


## 緣起

隨著前幾年多個出名的博客服務商終止運營，或者不再提供更新與維護，國文博客開始文藝復興，重新歸復到獨立建站，個中滋味大概只有國朝網民知曉。縱然外國亦有不少博客服務商，但在互聯網開始被爭霸之中的主權國家裹挾之今日，又有哪個主權國家更高尚一些嗎？除了內容自託管，我們別無選擇。

內容的完全控制和方便遷移，這是我託管博客的兩大目標。考慮到國朝網路運營商對 web 端口的屏蔽，如不映射到海外服務器，那麼在自家主機上開放 http/https 服務，在可預見的未來都是不可能的。為了達成這樣的目標，我選擇靜態網站託管服務與分布式儲存兩種方案。

選擇靜態網站的一個理由是不需要考慮數據庫的維護。相較於動態網站使用數據庫來存儲網站內容和用戶數據，靜態網站以靜態文件的形式存儲內容，進而減少了維護成本。個人使用 wordpress 的過程也有曾遭受過黑客攻擊的經歷，缺少安防經驗也促使我不再使用動態網站。除此之外，靜態網站也更易遷移，選擇靜態網站可方便地將數據從一個託管服務提供商遷移到另一個。

而選擇分布式儲存是由於國內網路環境的限制而不得已的選擇，同時也能有效增強自己對數據的控制權。分布式網站有很多方案，其中比較知名的有 [ZeroNet](https://zeronet.io/) 和 [IPFS](https://ipfs.tech/)，但 zeronet 目前已停止更新，我們的選擇可能只有 IPFS
了。

IPFS 全稱為 InterPlanetary File System （星際文件系統），是一個基於內容尋址的分布式存儲方案，目前有 [Brave瀏覽器](https://brave.com/) 支援使用 [ENS域名](https://ens.domains/) 或者傳統域名進行訪問 IPFS 網站。

我的網誌已經歷十幾年，雖然文章不多，但也歷經折騰，然而卻不曾介紹過我的部署方案。是時候將我的部署方案公之於眾了，以下供大家參考與批判。

## 靜態網站的部署

### 使用 hugo 生成靜態網站

我的博客選擇是靜態網站生成器 [hugo](https://gohugo.io/)  ，主題為 [DoIt](https://github.com/HEIGE-PCloud/DoIt)， DoIT 的基本配置可參考其[官方網站](https://hugodoit.pages.dev/zh-cn/theme-documentation-basics/)。

- 網誌文章的 markdown 文件放入 `$PWD/yoursite/content/posts` 
- 簡單頁面的 markdown 文件放入 `$PWD/yoursite/content/` 

md 文件的名稱就是改頁面的連結，示例如下：

- [三和大神的双十一怎么过？](https://sogola.com/posts/godsofsanhe1111/): `$PWD/yoursite/content/posts/godsofsanhe1111.md`
- [關於頁面](https://sogola.com/about/): `$PWD/yoursite/content/about.md`

對於使用 Hugo 創建博客文章，需要注意和普通 Markdown 文件的區別。在 Hugo 中，無法使用如同普通 markdown 文件般使用 # 來標記文章標題。相反，你需要在文件內使用前置參數進行標記。以下是一個示例前置參數：

````
title: "三和大神的双十一怎么过？"
date: 2019-11-11T11:52:18+08:00
lastmod: 2020-06-16T15:58:26+08:00
draft: false
author: "王小嗨"
summary: "双十一，我去三和做了一晚快递日结。"
featuredImage: "/images/godsofsanhe1111/20191111.1.jpg"
images: ["/images/godsofsanhe1111/20191111.1.jpg"]
toc: false
keepStatic: false
auto: true
categories: ["Capitalism","Life"]
tags: ["三和大神","日结","零工","快递"]
license: '<a rel="license external nofollow noopener noreffer" href="https://creativecommons.org/licenses/by-nc/3.0/" target="_blank">CC BY-NC 3.0</a>'
````

在這個示例中，`title` 屬性標示了文章的標題，`date` 標示了文章的發布日期和時間，`lastmod` 標示文章最後修改的時間，`draft` 屬性表示文章是否是草稿狀態。另外，還有一些其他的屬性，比如前面列舉的 `author`、`summary`、`featuredImage` 等等。在這個標題之後，可以使用正常的 Markdown 語法來編寫文章的內容。其中圖片文件在 `$PWD/yoursite/static/images` 目錄。

在安装 hugo 程式，下載好 DoIt 主題， 完善 `config.toml` 配置文件，寫好妳的文章之後，cd 至 `$PWD/yoursite` ，運行 `hugo server -D` 可對博客網站預覽，若滿意，則運行 `hugo` 命令。在 `$PWD/yoursite/public` 目錄中，妳將會得到博客的所有靜態文件。

### 將靜態網站上載至託管服務商

有了整個網站的靜態文件，我們需將其上傳至託管服務商。目前，可供選擇服務商有好幾家，大部分服務商的免費方案都足夠個人博客使用。

- [Vercel](https://vercel.com/)
- [Netlify](https://www.netlify.com/)
- [Cloudflare Pages](https://pages.cloudflare.com/)
- [GitHub Pages](https://pages.github.com/)
- [Fleek](https://fleek.co/)

以上服務商，都支持綁定 GitHub 倉庫，它會自動拉取最新倉庫更新並保持同步。其中 Fleek ，不僅支持生成 http 頁面，還會將文件上傳至 IPFS 網路，給出 CID。

先在 GitHub 新建一個 repository， 與本地的 `$PWD/yoursite/public` 目錄進行關聯，並將博客文件上載至倉庫。

````
cd $PWD/your_site/public
git init
git add .
git commit -m "deploy blog"
git branch -M main
git remote add origin git@github.com:your_github_name/your_blog_repository.git
git push -u origin main
````

之後，有新博文之後，再重新運行 `hugo server -D` 預覽博客，若滿意，則運行 `hugo` 命令更新博客的靜態文件，再將其更新至 GitHub 倉庫。

````
cd $PWD/yoursite/public
git add .
git commit -m "blog update"
git push -u origin main
````

`git push` 完成後，託管服務商會自動讀取倉庫更新，將其部署至服務器。

### 綁定傳統域名

在託管服務商創建我們的博客項目並綁定 GitHub 倉庫後，通常服務商會提示我們綁定域名。如若沒有相關提示，也可以在其管理界面中方便地找到綁定域名的選項，按照其要求添加相關 DNS 記錄，比如 A 記錄 或者 CNAME 記錄。

為了更方便、更安全地管理域名的 DNS，建議使用 [Cloudflare](https://www.cloudflare.com/) 服務。首先，在 Cloudflare 上添加域名，然後在域名服務商的管理界面中將 DNS 服務器變更為 Cloudflare 提供的 DNS 服務器，最後等待生效即可。

當然，如果不想購買域名的話，也可使用服務商提供的二級域名。但是，若使用自有域名，我們將更方便地將網站從一個服務商遷移至另一個，僅需更改 DNS 記錄即可。

## 博客的IPFS部署方案

### docker 安裝 IPFS 

將網誌託管給服務商固然很方便，這也增加了被審查與阻斷的風險。作為國朝臣民，我們天然地對任何服務商沒有信任。如果選擇 IPFS 方案託管網誌，這無疑將大大地治癒我們的書報檢查PTSD。但要特別注意：IPNS 並不匿蹤，書寫與言說就要承受利維坦之重，請審慎考量再使用鍵鼠。

IPFS 擁有一個域名系統 IPNS (InterPlanetary File System)，可讓我們更方便地配合域名使用 IPFS ， IPFS 託管網站的要義是用戶自己控制 IPNS 的 private key (私鑰)。儘管已有提供 IPNS 託管服務的選項，但這要付出信任成本。如若選擇自行生成與託管 IPNS Key ，這是一個 trustless 的抉擇，只要自己做好安全風險的管控。

要進行 IPFS 網站的自託管，首先要 安裝 IPFS 程式。因為需要 7*24 的不間斷服務，將 IPFS 安裝在功耗較低的小主機是一個好的選擇，所有選擇 docker 安裝 IPFS 是一個好的選項。

````
docker run -d --name ipfs \
  -v $PWD/docker/ipfs/ipfs:/data/ipfs \
  -v $PWD/docker/ipfs/export:/export \
  -u 1000:1000 \
  -p 4001:4001 \
  -p 8080:8080 \
  ipfs/go-ipfs:latest  
````

### 生成 IPNS Key ，並綁定域名

安裝完成後，進入 IPFS 容器的 Shell ，運行以下命令，將服務应用 lowpower 配置，這會優化 IPFS 資源的佔用情況。

````
ipfs config profile apply lowpower
````

接著在 Shell 中， cd 至 `export` 目錄，運行以下命令，生成一個 IPNS key pair。

````
ipfs key gen your_key_name
````

記錄給出的 IPNS 的 public key (公鑰)，並使用以下命令將其導出。

````
ipfs key export your_key_name
````

在文件管理中，進入至 `$PWD/docker/ipfs/export` ，將目錄中的 Key 文件妥善保管，以下是 IPFS KEY 管理的命令。

````
ipfs key export <name>           - Export a keypair
ipfs key gen <name>              - Create a new keypair
ipfs key import <name> <key>     - Import a key and prints imported key id
ipfs key list                    - List all local keypairs.
ipfs key rename <name> <newName> - Rename a keypair.
ipfs key rm <name>...            - Remove a keypair.
ipfs key rotate                  - Rotates the IPFS identity.
````

有了 IPNS Key 之後，就可將內容的 CID 發布到 IPNS 。如果直接訪問 IPFS 公鑰，當然也可以，但也這讓訪問者望而卻步，一大串字符讓人望而卻步。為了方便記憶，我們需要將 IPNS 與域名綁定，綁定方法可參考我的文章[Planet：一款IPFS静态站点生成器](https://sogola.com/posts/planet/)中的域名绑定綁定部分。

### 自動更新 IPNS

使用 IPFS 自託管靜態網站，主要是因為其分布式特性，它讓我們重拾萬維網之互聯真義。然而，由於個人博客的讀者不多，使用 IPFS 的用戶更是寥寥無幾，因此在 IPFS 自託管的同時，使用 remote pinning services 來提高網站的可用性是個不錯的選擇。

在介紹靜態網站託管服務商的時候，有提到 [Fleek](https://fleek.co/) ，它就有提供 remote pinning services ，在生成動態網站的同時，可生成一個網站的 CID。

如果我們在每次更新網站後，從 Fleek 生成的域名讀取出它綁定的 CID，並在本地 pin 這個 CID，然後再將其發布至我們自己的 IPNS ，那麼我們就可以實現同時更新靜態網站服務商和 IPNS 託管。所以，我請 ChatGPT 寫了可自動這一系列操作的腳本，如下所示。

````
#!/bin/bash
#
# Script: update-ipns.sh
# Description: Check if IPNS record needs to be updated and update it if necessary.

set -euo pipefail

# Function: get IPNS CID
get_ipns_cid() {
  local ipns_name=$1
  local ipfs_container=$2
  docker exec "${ipfs_container}" ipfs resolve -r "/ipns/${ipns_name}" \
    | tail -n1 \
    | awk '{ print $NF }'
}

# Variables: define container name, IPNS names and options
IPFS_CONTAINER="ipfs"
SOGOLA_IPNS="your_domain_name"
FLEEK_IPNS="your_fleek_site_name.on.fleek.co"
PIN_OPTS="--progress"
PUBLISH_OPTS="--key=your_ipns_public_key"

# Check if the IPFS container is running
if [ ! "$(docker ps -q -f name=${IPFS_CONTAINER})" ]; then
    echo "${IPFS_CONTAINER} container is not running. Please start the container and try again."
    exit 1
fi

# Get IPNS CID
SOGOLA_CID=$(get_ipns_cid "${SOGOLA_IPNS}" "${IPFS_CONTAINER}")
FLEEK_CID=$(get_ipns_cid "${FLEEK_IPNS}" "${IPFS_CONTAINER}")

# Check if IPNS record needs update
if [ "${SOGOLA_CID}" != "${FLEEK_CID}" ]; then
  # Publish new IPNS record
  docker exec "${IPFS_CONTAINER}" ipfs name publish "${PUBLISH_OPTS}" --lifetime=8766h -- "${FLEEK_CID}"
  # Unpin the previous IPNS record
  docker exec "${IPFS_CONTAINER}" ipfs pin rm "${SOGOLA_CID}"
  # Pin the updated IPNS record
  docker exec "${IPFS_CONTAINER}" ipfs pin add "${PIN_OPTS}" -- "${FLEEK_CID}"
  echo "IPNS record updated successfully."
else
  echo "IPNS record is already up-to-date, no need to update."
fi

# Add error handling
if [ $? -eq 0 ]; then
  echo "Update completed."
else
  echo "Error occurred while updating IPNS record. Please check."
  exit 1
fi
````

將上述腳本中的 SOGOLA_IPNS,FLEEK_IPNS,PUBLISH_OPTS 分別替換並保存為 `update-ipns.sh` ，使用自動化工具定時執行，即可使用 Git 更新博客後，實現靜態網站的 HTTP 與 IPFS 同時更新的功能。

如果覺得只有一個 remote pinning service 不保險的話，還可以使用 [4EVERLAND](https://dashboard.4everland.org?invite=BB0BLBGV) ，這個服務商支持使用 IPNS 進行 PIN。

## 總結

透過個間網站託管服務商，我們可以通過控制域名來遊走之間，輕鬆實現切換服務商。而通過自託管 IPFS ，拜分布式託管之幸，我們終可回歸到萬維網之本心，重新掌握我們自己的數據。

就撰寫這篇博文期間，我留意到[一則新聞](https://www.solidot.org/story?sid=74728)，ICANN 和 Verisign 提议允许任何政府扣押域名。是時候，我們該嘗試無許可與去信任的 crypto 。

> 整治書報檢查制度的真正而根本的辦法，就是廢除書報檢查制度，因為這種制度本身是惡劣的，可是各種制度卻比人更有力量。我們的意見可能是正確的，也可能是不正確的，不過無論如何，新的檢查令終究會使普魯士的作者要么獲得更多的現實的自由，要么獲得更多的觀念的自由，也就是獲得更多的意識。
> 
> Rara temporum felicitas, ubi quae velis sentire et quae sentias dicere licet［當你能夠想你願意想的東西，並且能夠把你所想的東西說出來的時候，這是非常幸福的時候］。
> 
> ————馬克思《評普魯士最近的書報檢查令》

「清風不識字」，主權宰制一切。當言說成為禁忌，開設一個部落格已然成為罪證，我只祝福妳們成為幸福的人。那些獨立博客的作者有福了，因為妳們必然承受大地之重。
