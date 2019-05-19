## Hugo教學：部落格網站

什麼是Hugo?是一個用 Go 語言編寫的靜態站點生成器，使用Hugo與Git來建立個人部落格。

![question_hugo](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/question_hugo.png)



以下分為兩部分介紹：**本地建立檔案、部署到 GitHub**。

## **本地建立檔案**

### 1.先安裝[hugo](https://gohugo.io/getting-started/installing/)，按照官網的步驟執行安裝(window、Mac都有寫)



### 2.用cmd在想要的路徑，建立網址的資料夾之後進入，例如:hugo_blog

```
hugo new site hugo_blog
cd hugo_blog
```



### 3.新增[部落格主題](<https://themes.gohugo.io/>)，例如:[hamburg](https://themes.gohugo.io/hugo-theme-hamburg/)為例，使用Git指令新增

```
git clone https://github.com/hauke96/hugo-theme-hamburg.git themes/hamburg
```

如果想要找主題的話，依據上面的連結點選Download，進入GitHub的repositories，將上面的網址改成主題的Github clone的網址，再將`themes/hamburg `改成`themes/你的主題名稱`。



### 4.建立新文章，例如:檔名為my-first-file

```
hugo new posts/my-first-file.md
```

在時檔案會在`content/posts`資料夾裡產生，此檔可使用任意文字編輯器打開markdown文件，將 draft 改成 false，文件內容任意更改。

以下為my-first-file.md內容：

```
---
title: "My First"
date: 2019-05-20T00:33:10+08:00
draft: false
---
測試部落格
```



### 5.新增留言版，建立[Disqus](https://help.disqus.com/)帳號，之後編輯config.toml

```
baseURL = "https://coolgood88142.github.io/" 
languageCode = "zh-tw"
title = "我的第一個部落格"
theme = "hamburg"

[params]
disqus = "Kai88142"
```

disqus放你的Disqus帳號的`Username`，**在Disqus要先填資料時要在語言的部分，填哪種語言留言板就顯示哪種呈現**，請在admin->Edit Settings->General裡的Language選擇Chinese，就可顯示中文樣式，目前Disqus沒有繁體中文只有簡體，先將就點用吧。



### 6.新增標籤，方便搜尋部落格資料

```
title: "My First"
date: 2019-05-20T00:33:10+08:00
draft: false
tags: ["First"]
```



### 7.編輯config.toml，baseURL改成你的github帳號名稱

```
baseURL = "https://coolgood88142.github.io/" #改成你github帳號名稱
languageCode = "zh-tw"
title = "我的第一個部落格" #標題可自行更改
theme = "hamburg" #主題名稱
```



### 8.本機測試

```
hugo server -D
```

執行後，在瀏覽器輸入網址`http://localhost:1313`，可看剛建立的部落格網站

![hugo-1](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/hugo-1.png)



![hugo-2](https://raw.githubusercontent.com/coolgood88142/markdown_note/master/assets/images/hugo-2.png)



## **部署到 GitHub**

### 1.在GitHub建立兩個repositories

在Github建立`hugo_blog`和`coolgood88142.github.io`(使用自己的GitHub帳號)



### 2.建立public資料夾

```
hugo
```

執行`hugo`指令，資料夾會自動產生public資料夾



### 3.將 public 資料夾上版到Github上的 coolgood88142.github.io

```
cd public
git init
git remote add origin https://github.com/coolgood88142/coolgood88142.github.io.git
git add .
git commit -m "my-first-hugo"
git push origin master
```



### 4.回到上一層將hugo_blog資料夾連結到GitHub 上的hugo_blog

```
cd ..
git init
git remote add origin https://github.com/coolgood88142/hugo_blog.git
```



### 5.將public 資料夾移除，之後建立public資料夾連結GitHub 上的 coolgood88142.github.io

```
rm -r public
git submodule add -f https://github.com/coolgood88142/coolgood88142.github.io.git public
```

這時public資料夾是新增`coolgood88142.github.io`的連結而不是專案



### 6.將hugo_blog資料夾上版

```
git add .
git commit -m "my-hugo-file"
git push origin master
```

上版後，在瀏覽器輸入網址`http://coolgood88142.github.io`，會看到跟本機看到的部落格是一樣的。



參考資料:[https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/540757/](https://codertw.com/程式語言/540757/)、<https://gohugo.io/getting-started/installing/>、<https://themes.gohugo.io/>、<https://themes.gohugo.io/hugo-theme-hamburg/>、[https://medium.com/@chs_wei/%E5%9C%A8-github-%E9%83%A8%E7%BD%B2-hugo-%E9%9D%9C%E6%85%8B%E7%B6%B2%E7%AB%99-9c40682dfe40](https://medium.com/@chs_wei/在-github-部署-hugo-靜態網站-9c40682dfe40)、https://gohugo.io/hosting-and-deployment/hosting-on-github/、http://coolgood88142.github.io