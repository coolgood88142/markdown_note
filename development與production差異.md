**development與production差異?**

webpack mode 模型分為兩種，development、production，

兩種模型底下含有插件(plugin)

**development(開發環境)**

plugin:

- NamedChunksPlugin:使用暫存(runtime)chunk物件都有一個id會進行更新
- NamedModulesPlugin:使用HMR時顯示模組的相對路徑

**為什麼development只有兩個?**

因為development為開發環境可當站台使用，所以只會有模組相對路徑與暫存變數進行更新，也可以使用自動編譯。

webpack-dev-server --mode development

webpack --watch --mode development

參考資料:<https://webpack.docschina.org/guides/development/>

**production(生產環境)**

plugin:

- FlagDependencyUsagePlugin:執行套件打包用到的内件檔案
- FlagIncludedChunksPlugin:檢測並標記模組之間的關係
- ModuleConcatenationPlugin:可將webpack所有模組轉換成一個模組
- NoEmitOnErrorsPlugin:可在編譯時發生錯誤回傳訊息
- OccurrenceOrderPlugin:預設為啟用，當webpack使用版本切換時，可確保刪除插件
- SideEffectsFlagPlugin:可執行webpack一個清除模組的指令，需要在package.json裡加"sideEffects": false
- UglifyJsPlugin:可簡化大量的程式碼進行壓縮並優化

參考資料:<https://segmentfault.com/a/1190000017387465>

**直接在package.json腳本指令輸入--mode development直接設定就不用在webpack.config.js一直變更。**

**node_env在哪裡使用?**

node_env為環境變數，當npm run webpack 執行時，webpack.config.js裡有設定mode為development或production會使用process.env.NODE_ENV變數決定用哪種

process.env.NODE_ENV === 'development'

process.env.NODE_ENV === 'production'

使用這兩種mode，development可以當站台使用或自動編譯，production可以將想要的套件打包輸出一個檔案(.js)，webpack.config.js沒設定時預設都會是production

development只做編譯速度比較快適用開發，production會將所有套件壓縮程式優化，速度會比較慢適用上線，因為檔案越小越好。

參考資料：

<https://stackoverflow.com/questions/49242756/how-to-set-mode-to-development-or-production-in-the-config-file>

<https://juejin.im/post/5a4ed5306fb9a01cbc6e2ee2>

<https://www.cnblogs.com/tugenhua0707/p/9780621.html>