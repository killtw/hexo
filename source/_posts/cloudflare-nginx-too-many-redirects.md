---
title: 'Cloudflare with nginx: Too many redirects'
date: 2016-08-05 13:04:43
tags: [ssl, cloudflare, nginx]
photos: /cloudflare-nginx-too-many-redirects/too-many-redirects.png
desc: 最近在試著將 blog 利用 Cloudflare 加上 CDN 的時候發現如果開啟 SSL 的功能時會發生如首圖 Too many redirects 的錯誤，研究了一下發現是因為 Cloutflare 的 Flexible SSL 搭配 nginx 時在某些情況下會產生這種狀況，也找到了幾種方式來解決這惱人的問題。
---

最近在試著將 blog 利用 [Cloudflare](https://www.cloudflare.com/) 加上 CDN 的時候發現如果開啟 SSL 的功能時會發生如首圖 **Too many redirects** 的錯誤，研究了一下發現是因為 Cloutflare 搭配 nginx 時在某些情況下會產生這種狀況，也找到了幾種方式來解決這惱人的問題。

<!-- more -->

在研究的過程中我一共找到三種不同的解法，基本上都不用去動到 nginx 的設定，分別是

1. [全面 strict 法](#strict)
2. [強制 https 法](#force-https)
3. [Page Rule strict 法](#page-rule-strice)

以下會做些簡單的步驟說明來介紹這三種方式要怎麼解決 **Too many redirects** 的問題

## <a id="strict"></a>全面 strict 法
如果你的 server 上原本就已經有 SSL 憑證的話，這應該是最無腦的方法了，只需要將 **Crypto** 裡頭的 SSL 設定從 Flexible 改成 Full(strict) 即可

![將 SSL 改為 Full(strict)](/cloudflare-nginx-too-many-redirects/strict.png)

## <a id="force-https"></a>強制 https 法
另外一個方式就是強制所有使用者在進入網站的時候都使用 https 的方式，Cloudflare 在這部份也有相當簡單的設置方法：

1. 點選上方的 **Page Rules**
![Page Rules](/cloudflare-nginx-too-many-redirects/page-rules.png)
2. Create Page Rule
3. 照著下圖選擇 **Always Use HTTPS**，網址的部份則是填入你希望使用 https 的網址並再後面加上星號來動態使用
![將網址強制使用 https](/cloudflare-nginx-too-many-redirects/force-https.png)

## <a id="page-rule-strice"></a>Page Rule strict 法
這個方法是結合了上面兩種來達成 HTTPS(strict)，利用 Page Rules 來針對特定網址將他改為 HTTPS(strict)，設定的方式也跟 [強制 https 法](#force-https) 相當類似
基本上的作法就是建個新的 Page Rule，然後將 SSL 改為 strict
![選擇 strice](/cloudflare-nginx-too-many-redirects/page-rules-strict.png)
這樣子就不會影響到其他的 subdomain 進而產生其他問題了

## 後記
基本上會發生這個問題是因為用 Flexible SSL 時，使用者進入到你的網站，nginx 還是會把它視為 http，如果你的 nginx 沒有特別判斷就轉址至 https 時就會產生無限迴圈
這三個方法中我自己是選用 [Page Rule strict 法](#page-rule-strice)，沒有什麼特別的原因，就只是想說既然有三個 Page Rule 的額度那就來用一下吧XD
如果你想從 nginx 那邊下手的話，可以用 `$http_x_forwarded_proto` 去判斷來源究竟是 http 還是 https 之後再進行轉址處理