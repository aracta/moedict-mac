# 萌典 Mac 版
教育部國語辭典轉換為 Mac 原生辭典計劃。

## Prerequisite

有裝 `brew` 的話直接 `brew install scala sbt` 就好了。否則手動安裝：

  * ☕ jdk ≥ 1.8
  * [scala 2.10.0+](http://www.scala-lang.org/downloads)
  * [sbt 0.13.0+](http://www.scala-sbt.org/release/docs/Getting-Started/Setup.html)
    第一次的話最好設定 `echo 'SBT_OPTS="-XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=512M -Xmx2g -Dfile.encoding=UTF8"' > ~/.sbtconfig` 以免發生 java.lang.OutOfMemoryError: PermGen space  
  * [Dictionary Development Kit](https://developer.apple.com/downloads/index.action) 登入後抓 Auxiliary Tools for Xcode。為了方便，我把 build 要用到的工具 copy 到 ./bin 裡面了，只是要 build 的話可以不用抓。

## How to Build
  用 sbt 一口氣執行到底：

        sbt get-db patch-db run build-dict

  或者是一步一步來：


  1. 先抓 kcwu 的資料庫

        curl http://kcwu.csie.org/~kcwu/moedict/dict-revised.sqlite3.bz2 | bzcat > dict-revised.sqlite3

  2. 使用 g0v 的 db2unicode.pl 將資料庫中以圖代字的 `<img src="xxxx">` 的轉換成 unicode

        curl -LO https://raw.github.com/g0v/moedict-epub/master/sym.txt
        curl -L https://raw.github.com/g0v/moedict-epub/master/db2unicode.pl | perl | sqlite3 development.unicode.sqlite3

  3. 將 development.unicode.sqlite3 轉換成 moedict_template/MoeDictionary.xml

        sbt run # 第一次跑 sbt 要抓很多東西會很久

  4. 利用 Apple 的 Dictionary Development Kit 將原始的 moedict_templates 的資料轉換成我們要的資料

        cd moedict_templates
        make # 等一陣子，在我的機器上要花 12mins
        make install 

## Reference

1. [Apple Dictionary Services Programming Guide](https://developer.apple.com/library/mac/#documentation/UserExperience/Conceptual/DictionaryServicesProgGuide/Introduction/Introduction.html)

2. [原始資料資料處理流程](https://github.com/g0v/moedict-process/wiki)
