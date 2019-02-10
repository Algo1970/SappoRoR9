### <img src="oreoreEHR/img/oreoreEHR.png" height="600" alt= "title"/>  


---
### <font id='p_title'>本日の内容</font>

1. <font id='p_title'>自己紹介</font>
2. <font id='p_title'>電子カルテ補助システムについて</font>
3. <font id='p_title'>実際のshinyアプリ</font>
4. <font id='p_title'>ここ1年の変更点</font>
5. shiny-serverの導入・注意点




---
### <font id='p_title'>自己紹介</font>

<div id="column300">
<img src="oreoreEHR/img/dog_circle.png" height="250" alt= "title"/>  
</div>
<div id="column660">
@fa[twitter fa-2x twitter-blue] @R_beginner <br>
仕事：内科開業医 <br>
趣味：<br>マラソン、R、電子カルテハッキング <br>
好きな開発環境:<br>Rstudio, neovim <br>
好きなRstudio shortcut：<br>alt + 矢印
</div>

---
### <font id='p_title'>電子カルテ補助システムについて</font>

電子カルテの写真？

+++
### <font id='p_title'>当院の？電子カルテの問題点</font>

- <font id='p_title'>患者ID毎に経時的な情報しか表示できない</font>
- <font id='p_title'>検索機能が貧弱</font>
- <font id='p_title'>各種診断スコア等の計算ができない</font>
- <font id='p_title'>次回予約プログラムが使いにくい</font>
- <font id='p_title'>ローカルネットワークのため、地図を表示できない</font>
<font id='p_title'>等など</font>

+++
### <font id='p_title'>これらの問題解決のため、</font>

+++
###  <font id='p_title'>shinyアプリ</font>

- <font id='p_title'>電子カルテに欲しい機能はshinyでつくる</font>
- <font id='p_title'>shiny-serverにのせて、24時間運用、<br>（Rstudioの邪魔にならない）</font>

+++
### <font id='p_title'>当院のネットワーク構成</font>

<img src="oreoreEHR/img/Clinic_Network2.png" height="500" alt= "title"/>  

+++
### <font id='p_title'>Rで接続</font>

<img src="oreoreEHR/img/RODBC.png" height="300" alt= "title"/>  

+++
### <font id='p_title'>サーバー内のテーブル</font>

- <font id='p_title'>患者マスター</font>
- <font id='p_title'>患者住所テーブル</font>
- <font id='p_title'>病名テーブル</font>
- <font id='p_title'>採血データテーブル</font>
- <font id='p_title'>処方・処置テーブル</font>
- <font id='p_title'>処方・処置テーブル</font>

+++
### <font id='p_title'>データは前処理必要</font>

- <font id='p_title'>前処理用のクラスファイル</font>

```
# PTaddress class----
PTaddress = setRefClass("PTaddress",
                        contains = c("MedicalStation"),
                        fields = list(data = "data.frame"),
                        methods = list(
                          initialize = function(id = NULL){
                            callSuper()  # 親クラスのコンストラクタを実行
                            if(!is.null(id)){
                              sql = paste("select personno, prefecture, city, town, aname 
                                          from PAddress as P inner join adrs as A on P.jiscode = A.jiscode 
                                          where personno=", as.character(id), ";")
                              con = .self$connect()
                              result = .self$sql2ms(con, sql)
                              result$personno  = as.numeric(result$personno )
                              colnames(result)[1] = 'id'
                              data <<- result
                            } else if(is.null(id)) {
                              con = .self$connect()
                              result = .self$sql2ms(con, "select personno, prefecture, city, town, aname 
                                                    from PAddress as P inner join adrs as A on P.jiscode = A.jiscode;")
                              result$personno  = as.numeric(result$personno )
                              colnames(result)[1] = 'id'
                              data <<- result
                            }
                          }
                        ))
# PTaddress methods----
(address = PTaddress$new(1014))
(address = PTaddress$new())
```


---
### <font id='p_title'>shiny appsの作り方</font>

<img src="oreoreEHR/img/shiny_site_book.png" height="500" alt= "title"/>  


---
### <font id='p_title'>shiny severの導入</font>

1. <font id='p_title'>R, Rstudioのインストール</font>
2. <font id='p_title'>shinyパッケージのインストール</font>
3. <font id='p_title'>shiny-serverのインストール </font>
```
sudo apt-get install gdebi-core
wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.9.923-amd64.deb
sudo gdebi shiny-server-1.5.9.923-amd64.deb
```

+++
### <font id='p_title'>ブラウザでアクセス</font>

`IPaddresss:3838`にアクセスするだけ。

<img src="oreoreEHR/img/shiny_top.png" height="500" alt= "title"/>  


---
+++
### 画面いっぱいにカルテ表示すると待ち患者が見えない…

+++
### 診察待ち患者アラート

<img src="oreoreEHR/img/alert.png" height="250" alt= "title"/>  

+++
### 長期処方で予約日が決めにくい

+++
@snap[west span-50]
<img src="oreoreEHR/img/ms_yoyaku.gif" height="500" alt= "title"/>  
@snapend
@snap[east span-50]
<img src="oreoreEHR/img/shiny_yoyaku.gif" height="500" alt= "title"/>  
@snapend

+++
@snap[west span-50]
<img src="oreoreEHR/img/ms_yoyaku.gif" height="500" alt= "title"/>  
@snapend
@snap[east span-50]
<img src="oreoreEHR/img/shiny_yoyaku2.gif" height="500" alt= "title"/>  
@snapend



+++
### 体重の経過が見にくい
### 必要栄養量の計算が面倒

+++
### 体重経過と必要栄養量

<img src="oreoreEHR/img/BWplot.png" height="500" alt= "title"/>  

+++
### 糖尿病患者A「薬を辞めてもいいですか？」

+++
### eGFRプロット

<img src="oreoreEHR/img/eGFRplot.png" height="500" alt= "title"/>  

+++
### 患者B「インフルエンザのワクチンは<br>いつ接種したら良いですか？」

+++
### Flu患者数

<img src="oreoreEHR/img/fluplot.png" height="500" alt= "title"/>  

+++
### 患者D「私の肝臓は大丈夫でしょうか？」

+++
### mayoPBCmodel

<img src="oreoreEHR/img/mayoPBCmodel.png" height="300" alt= "title"/>  

+++
### MELD

<img src="oreoreEHR/img/MELD.png" height="300" alt= "title"/>  

+++
### predict Liver Transplant

<img src="oreoreEHR/img/predict_LT.png" height="300" alt= "title"/>  

+++
### 会計士「新患が減っていますが、10年後は大丈夫ですか？」

+++
### pyramid

<img src="oreoreEHR/img/pyramid.jpg" height="500" alt= "title"/>  

+++
### pyramid DM

<img src="oreoreEHR/img/pyramid_DM.jpg" height="500" alt= "title"/>  

+++
### 患者G「C型肝炎の薬は、どれぐらい効くの？」
### 患者H「C型肝炎の薬は、どう違うのですか？」

+++
### Rmarkdownで患者説明資料

+++?image=oreoreEHR/img/Rmd_HCV.png&size=contain


+++
### 患者G「どうして豚肉は生で食べちゃいけないの？」


+++?image=oreoreEHR/img/Rmd_HEV.png&size=contain

---
### 患者住所テーブル

- local networkのため、地図表示ができず、患者位置情報を利用できない。

-> 自前MAP server

+++
### 自前マップサーバーを立てよう

<img src="oreoreEHR/img/OpenMapTile.jpg" height="500" alt= "title"/> 

<p id="p_small_gray">https://openmaptiles.com/</p>


+++
### <fong id='p_bold_olive'>患者C「近くによい皮膚科はありますか？」</font>
### <fong id='p_bold_olive2'>「近くに皮膚科はありますか？」</font>
### <fong id='p_bold_olive3'>「近くに皮膚科はありますか？」</font>
### <fong id='p_bold_olive4'>「近くに皮膚科はありますか？」</font>

+++
### <fong id='p_bold_olive'>patient map</font>

<img src="oreoreEHR/img/ms_map.jpg" height="500" alt= "title"/>  

+++
### clinic map

<img src="oreoreEHR/img/map_hp.jpg" height="500" alt= "title"/>  


---
### 最近の変更点

- DB接続、テーブルごとの前処理までをRの参照クラスで書き換え
- shiny用snippet作成で、開発効率アップ

+++
### 参照クラスの例

```
library(RODBC)
# Rsqlserver class----
Rsqlserver = setRefClass("Rsqlserver",
                          fields = list(dsn = "character",
                                        uid = "character",
                                        pwd = "character"
                          ),
                          methods = list(
                            initialize = function(dsn = "hoge_server", uid = "hoge_id", pwd = "hoge_pass"){
                              .self$dsn = dsn
                              .self$uid = uid
                              .self$pwd = pwd
                            },
                            connect = function(){
                              RODBC::odbcConnect(.self$dsn, .self$uid, .self$pwd)
                            }
                          ))
```

+++
### shiny用スニペットの例

```
snippet msClassBase
	library(RODBC)${0}
	# Rsqlserver class----
	Rsqlserver = setRefClass("Rsqlserver",
				fields = list(dsn = "character",
					uid = "character",
					pwd = "character"),
				methods = list(
					initialize = function(dsn = "hoge_server", uid = "hoge_id", pwd = "hoge_pass"){
						.self\$dsn = dsn
						.self\$uid = uid
						.self\$pwd = pwd},
					connect = function(){
						RODBC::odbcConnect(.self\$dsn, .self\$uid, .self\$pwd)
					}
				))
```

---
### shiny-serverの導入
`shinyserver.sh`
```
#!/bin/sh

# install shiny-server
sudo apt-get install gdebi-core
wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.9.923-amd64.deb
sudo gdebi shiny-server-1.5.9.923-amd64.deb
```

`srv/shiny-server/sample-apps/`フォルダに`ui.R`と`server.R`の入ったフォルダごと入れる。
`<ip address>:3838/sample-apps`にアクセスするだけ。

---
### shiny-serverの注意点

1. sample-appsのアクセス権限、chmod -R 777 hoghoge
2. 環境変数、スコープ
3. libpathの違い
4. マルチプロセスは、他サイトを参照。少人数なら同じアプリをフォルダ名変えて作成して対応

+++
### library path

<img src="oreoreEHR/img/libpath.png" height="500" alt= "title"/>  






---?color=#6b8e23




+++?color=#6b8e23
### まとめ


---
### その他

- リモートログオンクライアントで、画像サーバー、駐車場モニター、Rstudioサーバーの管理







