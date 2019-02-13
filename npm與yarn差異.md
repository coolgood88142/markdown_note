**npm與yarn差異?**

npm與yarn一樣都是套件管理工具，但是yarn的安裝速度比npm快很多，使用指令上也有差異，yarn必須從package.json安裝，安裝套件時有一個yarn.lock文件，當套件想更換版本時只要變更yarn.lock文件就可隨時更換版本，npm必須依賴package.json手動更換版本安裝



**yarn**優點

**離線模式**

 yarn會有一個快取目錄，會快取以前安裝過的軟體包，再次安裝時就不必從網路下載了，大大加速安裝速度。

**依賴關係確定性**

npm在執行安裝套件都會依賴別的套件版本，導致有重新安裝的問題，yarn在安裝時依賴結構是一致的

**網路效能優化**

yarn在安裝套件時會優化請求順序。

**網路回彈**

yarn在某個安裝包請求失敗時不會導致安裝失敗，它會自動去嘗試重新安裝。而npm則會毫不猶豫的失敗，導致得再來一次，耗費時間

**扁平模式**

對於多個包依賴同一個子包的情況，yarn會盡量提取為同一個包，防止出現多處副本，浪費空間。比如1.2中，yarn會為babel-generator和babel-helper-define-map 建立同一個lodash子依賴，這樣就節約一份的空間。

yarn安裝時需要先npm，yarn是依賴node環境執行，npm install yarn -g



參考資料:https://www.zcfy.cc/article/yarn-vs-npm-everything-you-need-to-know-1484.html

https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/251371/

https://www.jeffjade.com/2017/12/30/135-npm-vs-yarn-detial-memo/