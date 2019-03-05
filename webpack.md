# webpack

webpack是來自於npm底下其中一個套件，主要是3個步驟：輸入、編譯、輸出

1. entry(入口點)：指定路徑的檔案做打包
2. output(出口)：將入口點所有檔案打包成一個檔案並輸出指定路徑
3. loader：針對套件的語言進行轉換成javascript，必需要安裝套件專用的loader套件進行轉換。
4. plugins(插件)：使用require可以自行定義使用多個插件同一個檔案。
5. mode(模型)：只有development或production來設定mode參數

webpack.config.js內容

```
//宣告常數 path為webpack設定檔路徑
const path = require('path');

//宣告常數 webpack為webpack設定檔路徑
const webpack = require('webpack');			

//設定檔匯出函數
module.exports = {
	//輸入指定entry.js檔案
	entry: './entry.js',
	
	//輸出bundle.js檔案
    output: {
    	//輸出檔案名稱為bundle.js
        filename: 'bundle.js',
        
        //定義檔案根目錄，將相對路徑換成絕對路徑
        path: path.resolve(__dirname, './'),
    },
    //自訂模組
	module: {
		//陣列存放物件
        rules: [
            {
            	//指定msg.vue檔(路徑)
                test: '/.msg.vue',
                //要轉換哪個loader(插件)
                use: 'vue-loader'
            }
        ]
    },
    //解析模組
	resolve: {
		//原本模組上的vue替換成新的路徑指定檔案
        alias: {
			'vue': "vue/dist/vue.js"
        },
        //可以省略js、vue
		extensions: ['.js', '.vue']
    }
};

//
```

參考資料：https://www.webpackjs.com/concepts/

https://ithelp.ithome.com.tw/articles/10193788

https://segmentfault.com/a/1190000013176083

https://rhadow.github.io/2015/03/23/webpackIntro/