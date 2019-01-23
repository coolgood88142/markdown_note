# webpack

webpack是來自於npm底下其中一個套件，主要是3個步驟：輸入、編譯、輸出

1. entry(入口點)：指定路徑的檔案做打包
2. output(出口)：將入口點所有檔案打包成一個檔案並輸出指定路徑
3. loader：針對套件的語言進行轉換成javascript，必需要安裝套件專用的loader套件進行轉換。
4. plugins(插件)：使用require可以自行定義使用多個插件同一個檔案。
5. mode(模型)：只有development或production來設定mode參數

```
const path = require('path');	//宣告常數 path為
const webpack = require('webpack');

module.exports = {
	entry: './entry.js',						//入口指定entry.js檔案
    output: {
        filename: 'bundle.js',					//輸出bundle.js檔案
        path: path.resolve(__dirname, './'),
    },
	module: {
        rules: [
            {
                test: '/.msg.vue',
                loader: 'vue-loader'
            }
        ]
    },
	resolve: {
        alias: {
			'vue': "vue/dist/vue.js"
        },
		extensions: ['.js', '.vue']
    }
};

//
```

參考資料：https://www.webpackjs.com/concepts/