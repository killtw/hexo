---
title: Yarn global add with nvm
tags: [node, nvm, yarn]
date: 2017-06-21 14:49:33
photos: /yarn-global-add-with-nvm/command-not-found.png
desc:
---
昨天心血來潮想說把 Blog 搬回 Github 上省下 linode 的費用時，發現 `hexo-cli` 由於用 nvm 裝了不同版本 node 沒有裝回 global packages 而不存在，這時只能重新安裝一次。

自從有了 yarn 之後就很少在用 npm 安裝 package，所以就很順手的輸入了 `yarn global add hexo-cli`，安裝的過程很順利，不過在執行 `hexo` 時卻發現一直出現 `command not found: hexo` 的訊息，到處找也都找不到究竟安裝到哪裡去，查了一下資料也沒有找到發生的原因。

後來在 Github 上找到了三個解決方法，其中兩個的作法是指定 global bin 的安裝位置，另一個則是在 PATH 中增加路徑，選擇其一即可。

<!-- more -->

1. [global-folder](#global-folder)
2. [config prefix](#config-prefix)
3. [add path](#add-path)

## <a id="global-folder"></a>global-folder
這個作法比較麻煩，因為每次要新增 global package 的時候都要在後面加上 `--global-folder=$(yarn global bin)`，當然也可以新增 alias 來達到相同目的。
比如說要安裝 `hexo-cli` 時就要輸入 `yarn global add hexo-cli --global-folder=$(yarn global bin)` 這樣。

## <a id="config-prefix"></a>config prefix
這個方式跟上面那個比起來相對簡單，而且只要作一次就可以，之後除非是安裝了不同版本的 node，不然不需要在做一次
直接執行 `yarn config set prefix $(npm config get prefix)` 之後再重新安裝 package 就可以發現執行檔安裝到了正確位置，並且可以執行了。

## <a id="add-path"></a>add path
後來發現這個方法比起上面兩個又更輕鬆，而且之後就算 node 版本有更動也不需要在做一次同樣動作
在你的 `.bashrc` 或是 `.zshrc` 等設定檔中的 PATH 部分加入 `yarn global bin` 的路徑，如下：
`export PATH="$PATH:$HOME/.config/yarn/global/node_modules/.bin"`