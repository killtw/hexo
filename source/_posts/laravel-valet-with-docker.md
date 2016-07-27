---
title: Laravel Valet with Docker
desc: 前一陣子 Laravel 官方推出了輕量化的開發環境解決方案 Valet，他跟 Homestead 最大的不同在於他不是一個完整的 VM，基本上他只有用 Caddy 作為 Web Server 以及 Dnsmasq 來管理網址，也因為使用 Caddy 的關係，所以目前支援的系統只有 Mac。
photos: /laravel-valet-with-docker/laravel-valet-with-docker.png
date: 2016-06-29 11:41:43
tags:
  - laravel
  - php
  - docker
---

前一陣子 [Laravel](https://laravel.com/) 官方推出了輕量化的開發環境解決方案 [Valet](https://laravel.com/docs/master/valet)，他跟 [Homestead](https://laravel.com/docs/master/homestead) 最大的不同在於他不是一個完整的 VM，基本上他只有用 Caddy 作為 Web Server 以及 Dnsmasq 來管理網址，也因為使用 Caddy 的關係，所以目前支援的系統只有 Mac。

<!-- more -->

由於輕量化的設計，Valet 並未包含任何 Database 跟程式語言，所以這部份我想到應該可以用 [Docker](https://www.docker.com/) 來簡單的處理掉，就順手研究了一下作法，接下來會根據以下步驟進行。

1. [Install Homebrew](#install-homebrew)
2. [Install Composer](#install-composer)
2. [Install Valet](#install-valet)
3. [Install Docker for Mac](#install-docker-for-mac)
4. [Docker config](#docker-config)

## <a id="install-homebrew"></a>Install Homebrew
[Homebrew](http://brew.sh/) 可以說是目前 Mac 上最方便的 Package Management 方案，我自己是連 App 的部份都會透過 Homebrew cask 來安裝，這樣每次重新安裝電腦的時候都可以直接透過一個指令安裝完所有的軟體。
安裝的步驟也非常簡單，只需要執行下面的 script 就能完成安裝。
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## <a id="install-composer"></a>Install Composer
[Composer](https://getcomposer.org/) 是 PHP 的 Dependency Manager，在 Packagist 上有許多開源的套件可以供大家使用，Laravel 以及接下來會用到 Valet 都可以在上面找到並使用。
```bash
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" #下載 Composer Setup script
php -r "if (hash_file('SHA384', 'composer-setup.php') === 'e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" #驗證 SHA-384
php composer-setup.php #執行安裝
php -r "unlink('composer-setup.php');" #移除 composer-setup.php
```
安裝完之後建議執行以下 script 方便之後使用
```bash
mv composer.phar /usr/local/bin/composer
```
最後在你的 `.bashrc` 或是 `.zhsrc` 內中加入
```bash
export PATH="$PATH:$HOME/.composer/vendor/bin"
```
這樣子系統才有辦法知道 Composer package 的路徑以便使用。

## <a id="install-valet"></a>Install Valet
上面講到 Valet 屬於 Composer package，所以他的安裝是需要透過 Composer 指令來進行
```bash
composer global require laravel/valet
```
安裝完 Package 之後執行
```bash
valet install
```
進行 Valet 的安裝動作，安裝過程中 Valet 會幫你透過 Homebrew 安裝 PHP 7.0 以及其他相關套件。

## <a id="install-docker-for-mac"></a>Install Docker for Mac
到這邊為止 Valet 已經算是安裝完成了，你可以查看 [官方文件](https://laravel.com/docs/master/valet) 來了解 Valet 的使用方式。
接下來我們會進行 Docker 的安裝，目前 [Docker for Mac](https://docs.docker.com/docker-for-mac/) 還處於 public beta 的階段，所以大家可以自行斟酌要不要使用XD，安裝方法也很簡單，只需要將 https://download.docker.com/mac/beta/Docker.dmg 下載之後正常安裝即可。
安裝完基本上不需設定，執行之後就可以看到 Docker 已經啟動，不過你還是可以針對 Cpu 以及記憶體的部份自行設定，一般是照預設就很夠用了。

## <a id="docker-config"></a>Docker config
目前我在 Docker 裡頭配置了 MariaDB、Redis 以及 Elasticsearch，語言的部份則有 NodeJS 以及 Ruby。
安裝方法也不難，基本上也只有幾個步驟而已：
```bash
git clone https://github.com/killtw/docker.git
```
假如你的系統沒有 `git` 的話也可以透過 `https://github.com/killtw/docker/archive/master.zip` 下載之後解壓縮使用。
`git clone` 或解壓縮之後在目錄內執行
```bash
docker-compose up -d redis mariadb elasticsearch
```
這樣就會將 MariaDB、Redis、Elasticsearch 跑起來，假如你有不需要的服務的話在指令中將他拿掉就可以了。
預設的帳號密碼可以從 `docker-compose.yml` 中修改。

語言的使用部份有兩種方式，一是直接執行指令，二則需要在 `.bashrc` 或 `.zshrc` 中加入 `alias` 來使用：

### NodeJS
```bash
docker run -it --rm -v $(PWD):/workspace killtw/workspace node
```
### NPM
```bash
docker run -it --rm -v $(PWD):/workspace killtw/workspace npm
```

基本上就是最後在改變，目前可以是用的有 `node, npm, gulp, bower, ruby`
假如不想每次都打那麼長的指令的話，可以在 `.bashrc` 或 `.zshrc` 中加入
```bash
alias node="docker run -it --rm -v $(PWD):/workspace killtw/workspace node"
```

## 後記
基本上這樣子該有的東西都有了，就可以開心的開發 Laravel Application 啦～
Docker 的部份還不是很熟，而且用法也跟官方比較推薦的 per project 不太一樣，如果大家有更好的作法的話歡迎告訴我，或是直接在 [Github](https://github.com/killtw/docker) 開 issue 給我。
