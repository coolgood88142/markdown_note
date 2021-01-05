---
title: "Configure Hugo"
date: "2019-05-12"
author: "Hugo"
summary: "說明Hugo要怎麼設定"
categories: ["config"]
tags: ["Hugo","config"]
---

什麼是Hugo?是一個用 Go 語言編寫的靜態站點生成器，使用Hugo與Git來建立個人部落格。

以下介紹Hugo 設定配置

#### Configuration File

Hugo 默認這三種 `config.toml`, `config.yaml`, or `config.json`格式來設定。

使用命令行`--config`開關選擇使用一個或多個配置文件覆蓋設定。

```
hugo --config debugconfig.toml
hugo --config a.toml,b.toml,c.toml
```

#### Configuration Directory

除了使用單個配置文件，可以用configDir目錄來設定配置

- 每個文件代表一個配置根目錄

- 每個目錄包含一組文件，這些文件包含特定於環境的設置。

- 可以對文件進行本地化以使其成為特定於語言的文件。

- config.toml

  ```
  ├── config
  │   ├── _default
  │   │   ├── config.toml
  │   │   ├── languages.toml
  │   │   ├── menus.en.toml
  │   │   ├── menus.zh.toml
  │   │   └── params.toml
  │   ├── production
  │   │   ├── config.toml
  │   │   └── params.toml
  │   └── staging
  │       ├── config.toml
  │       └── params.toml
  ```

#### Configure Build 

設定build配置

```
config.toml
[build]
  noJSConfigInAssets = false
  useResourceCacheWhen = "fallback"
  writeStats = false
```

- noJSConfigInAssets：設定`js.Build`將`jsconfig.json`寫入`/assets`文件夾的操作。
- useResourceCacheWhen：在`/resources/_gen`使用PostCSS和ToCSS。有效值為`never`、`always`和`fallback`。
- writeStats：啟用後，將hugo_stats.json與有關構建的一些的資料寫入根目錄。

#### Configure Server

設定server，執行hugo server時，允許開發過程中設定HTTP。配置格式與[Netlify](https://docs.netlify.com/routing/headers/#syntax-for-the-netlify-configuration-file)格式一樣

```
config.toml
[server]
  [[server.headers]]
    for = "/**.html"
    [server.headers.values]
      Content-Security-Policy = "script-src localhost:1313"
      Referrer-Policy = "strict-origin-when-cross-origin"
      X-Content-Type-Options = "nosniff"
      X-Frame-Options = "DENY"
      X-XSS-Protection = "1; mode=block"
```

可以在development目錄裡下的server.toml

```
config/development/server.toml
[[headers]]
  for = "/**.html"
  [headers.values]
    Content-Security-Policy = "script-src localhost:1313"
    Referrer-Policy = "strict-origin-when-cross-origin"
    X-Content-Type-Options = "nosniff"
    X-Frame-Options = "DENY"
    X-XSS-Protection = "1; mode=block"
[[redirects]]
  force = false
  from = "/myspa/**"
  status = 200
  to = "/myspa/"
```

#### Configure Title Case

設置`titleCaseStyle`標題大寫默認為[AP Stylebook](https://www.apstylebook.com/)，但您也可以將其設置為`Chicago`或`Go`（每個單詞都以大寫字母開頭）。

#### Configuration Environment Variables

HUGO_NUMWORKERMULTIPLIER可以設置為增加或減少在Hugo中用於並行處理的工人數量。如果未設置，將使用邏輯CPU的數量。

#### Configuration Lookup Order

在根目錄中搜索配置文件，默認這三種文件

- `./config.toml`
- `./config.yaml`
- `./config.josn`

#### Example Configuration(範例)

```
config.toml
baseURL = "https://yoursite.example.com/"
footnoteReturnLinkContents = "↩"
title = "My Hugo Site"

[params]
  AuthorName = "Jon Doe"
  GitHubUser = "spf13"
  ListOfFoo = ["foo1", "foo2"]
  SidebarRecentLimit = 5
  Subtitle = "Hugo is Absurdly Fast!"

[permalinks]
  posts = "/:year/:month/:title/"
```



#### Module Config: mounts(掛載)

```
[module]

  [[module.mounts]]
    source = "content"
    target = "content"

  [[module.mounts]]
    source = "static"
    target = "static"

  [[module.mounts]]
    source = "layouts"
    target = "layouts"

  [[module.mounts]]
    source = "data"
    target = "data"

  [[module.mounts]]
    source = "assets"
    target = "assets"

  [[module.mounts]]
    source = "i18n"
    target = "i18n"

  [[module.mounts]]
    source = "archetypes"
    target = "archetypes"
```

content、static、layouts、data、assets、i18n、archetypes，設定hugo資料夾路徑，source是指來



參考資料:

[https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/540757/](https://codertw.com/程式語言/540757/)、

<https://gohugo.io/getting-started/installing/>、

<https://themes.gohugo.io/>、

<https://themes.gohugo.io/hugo-theme-hamburg/>、

[https://medium.com/@chs_wei/%E5%9C%A8-github-%E9%83%A8%E7%BD%B2-hugo-%E9%9D%9C%E6%85%8B%E7%B6%B2%E7%AB%99-9c40682dfe40](https://medium.com/@chs_wei/在-github-部署-hugo-靜態網站-9c40682dfe40)、

https://gohugo.io/hosting-and-deployment/hosting-on-github/、

http://coolgood88142.github.io、

<http://m.tooopen.com/view/520372.html>