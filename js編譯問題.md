### js編譯問題

#### 問題

- laravel-mix版本：先確認好laravel-mix版本，如果是6.0以上，請照[官網](https://laravel-mix.com/docs/6.0/upgrade)的步驟進行調整

  ```json
  //package.json
  "scripts": {
      "development": "mix",
      "watch": "mix watch",
      "watch-poll": "mix watch -- --watch-options-poll=1000",
      "hot": "mix watch --hot",
      "production": "mix --production"
  }
  ```

  ```javascript
  //webpack.mix.js
  mix.js('resources/js/app.js', 'public/js').vue();
  mix.js('resources/js/app.js', 'public/js').react();
  ```

  如果6.0以下的版本，就直接安裝不影響編譯

- cross-env編譯問題

  重新安裝cross-env，裝完後確認package.json的scripts有個NODE_ENV參數，有沒有寫cross-env的js檔案

  ```bash
  npm install cross-env --save-dev
  ```

  
