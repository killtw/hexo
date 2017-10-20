---
title: Whenever with Capistrano 3
tags: [ruby, rails, capistrano, whenever]
date: 2017-10-20 12:09:42
photos: /whenever-with-capistrano-3/CapistranoLogo.png
desc: 最近在五倍的 Rails 案子中碰到了使用 Capistrano 部屬中，bundle exec whenever 會一直出現 bundle command not found 的問題。
---

最近在五倍的 Rails 案子中碰到了使用 [Capistrano](http://capistranorb.com/) 部屬中，`bundle exec whenever` 會一直出現 `bundle: command not found` 的問題，跟同事檢查了一陣子都找不到問題所在。

由於專案的需求，Ruby 的路徑是使用環境變數來設定，一般而言在其他 command 都能順利執行的情況下應該是不會出現這樣的問題，唯一能想到的就是可能在執行的時候沒有吃到自訂的環境變數，在 Capistrano 中是使用 `set :default_env` 來設定環境變數，在檢查程式碼的時候發現是 [whenever](https://github.com/javan/whenever) 執行的過程中根本沒有去讀取自訂的環境變數

<!-- more -->

``` ruby
set :whenever_command_environment_variables, ->{ { rails_env: fetch(:whenever_environment) } }
```

其實這個問題的解決辦法也很簡單，就是把原本設定的環境變數加到 `whenever_command_environment_variables` 裡頭讓他在執行時可以吃到，關於這個部分我已經發了 PR 就看 owner 什麼時候想要解決，而短期的作法如下：

``` ruby
set :whenever_command_environment_variables, -> { fetch(:default_env).merge!(rails_env: fetch(:whenever_environment)) }
```

把以上這一段加入到 `deploy.rb` 中或是 stage 的設定檔中就可以了。
嗯，寫完了一篇費文呢✌️
