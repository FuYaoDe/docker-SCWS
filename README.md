# docker-SCWS

開源簡易分詞系統 Docker 版，此版本僅提供 cli 的方式執行，方便使用者不用重新編譯能直接使用。

# SCWS 簡介
[SCWS](http://www.xunsearch.com/scws) 是 Simple Chinese Word Segmentation 的首字母縮寫（即：簡易中文分詞系統）。
這是一套基於詞頻詞典的機械式中文分詞引擎，它能將一整段的中文文本基本正確地切分成詞。詞是
中文的最小語素單位，但在書寫時並不像英語會在詞之間用空格分開，所以如何準確并快速分詞一直
是中文分詞的攻關難點。

SCWS 採用純 C 語言開發，不依賴任何外部庫函數，可直接使用動態鏈接庫嵌入應用程序，支持的
中文編碼包括 `GBK`、`UTF-8` 等。此外還提供了 PHP 擴展模塊，可在 PHP 中快速
而方便地使用分詞功能

分詞算法上並無太多創新成分，採用的是自己採集的詞頻詞典，並輔以一定的專有名稱，人名，地名，
數字年代等規則識別來達到基本分詞，經小範圍測試準確率在 90% ~ 95% 之間，基本上能滿足一些
小型搜索引擎、關鍵字提取等場合運用。首次雛形版本發佈於 2005 年底。

SCWS 由 [hightman](http://www.hightman.cn) 開發，並以 BSD 許可協議開源發布 ，參見 [COPYING](https://github.com/hightman/scws/blob/master/COPYING)

# 使用方法
```bash
$ docker run --rm -it scws:latest scws -h
$ docker run --rm -it scws:latest scws -i '这是分词的命令行工具' -c utf8 -d ./dict.utf8.xdb
```
### 使用自訂的辭典
```bash
$ docker run --rm -it -v $(pwd)/XDB/dict.utf8.xdb:/custom.xdb scws-dev:0.0.3 scws -i '这是分词的命令行工具' -c utf8 -d /custom.xdb
```

### scws 這是分詞的命令行工具，執行 scws -h 可以看到詳細幫助說明。

 ```
 Usage: scws [options] [[-i] input] [[-o] output]
 ```
 
 * _=-i string|file_ 要切分的字符串或文件，如不指定則程序自動讀取標準輸入，每輸入一行執行一次分詞
 * _-o file_ 切分結果輸出保存的文件路徑，若不指定直接輸出到屏幕
 * _-c charset_ 指定分詞的字符集，默認是 gbk，可選 utf8
 * _-r file_ 指定規則集文件（規則集用於數詞、數字、專有名字、人名的識別）
 * _-d file[:file2[:...]]_ 指定詞典文件路徑（XDB格式，請在 -c 之後使用）
 
 ```
 自 1.1.0 起，支持多詞典同時載入，也支持純文本詞典（必須是.txt結尾），多詞典路徑之間用冒號(:)隔開，
 排在越後面的詞典優先級越高。

 文本詞典的數據格式參見 scws-gen-dict 所用的格式，但更寬鬆一些，允許用不定量的空格分開，只有<詞>是必備項目，
 其它數據可有可無，當詞性標註為“!”（嘆號）時表示該詞作廢，即使在較低優先級的詞庫中存在該詞也將作廢。
 ```
 * _-M level_ 複合分詞的級別：1~15，按位異或的 1|2|4|8 依次表示 短詞|二元|主要字|全部字，缺省不復合分詞。
 * _-I_ 輸出結果忽略跳過所有的標點符號
 * _-A_ 顯示詞性
 * _-E_ 將 xdb 詞典讀入內存 xtree 結構 (如果切分的文件很大才需要)
 * _-N_ 不顯示切分時間和提示
 * _-D_ debug 模式 (很少用，需要編譯時打開 --enable-debug)
 * _-U_ 將閒散單字自動調用二分法結合
 * _-t num_ 取得前 num 個高頻詞
 * _-a [~]attr1[,attr2[,...]]_ 只顯示某些詞性的詞，加~表示過濾該詞性的詞，多個詞性之間用逗號分隔
 * _-v_ 查看版本

### scws-gen-dict 詞典轉換工具

 ```
 Usage: scws-gen-dict [options] [-i] dict.txt [-o] dict.xdb
 ```
 * _-c charset_ 指定字符集，默認為 gbk，可選 utf8
 * _-i file_ 文本文件(txt)，默認為 dict.txt
 * _-o file_ 輸出 xdb 文件的路徑，默認為 dict.xdb
 * _-p num_ 指定 XDB 結構 HASH 質數（通常不需要）
 * _-U_ 反向解壓，將輸入的 xdb 文件轉換為 txt 格式輸出 （TODO）

 > 文本詞典格式為每行一個詞，各行由 4 個字段組成，字段之間用若干個空格或製表符(\t)分隔。
 > 含義（其中只有 <詞> 是必須提供的），`#` 開頭的行視為註釋忽略不計：
 > ```
 > #<詞> <詞頻(TF)> <詞重(IDF)> <詞性(北大標註)>
 > 新詞條 12.0 2.2 n
 > ```

### [更多詳細說明](https://github.com/hightman/scws)
### [官方網站](http://www.xunsearch.com/scws/index.php)
