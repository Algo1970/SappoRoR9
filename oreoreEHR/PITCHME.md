### <img src="oreoreEHR/img/oreoreEHR.png" height="600" alt= "title"/>  


---
### 本日の内容

1. 自己紹介
2. 電子カルテ補助システムについて
3. 実際のshinyアプリ
4. ここ1年の変更点
5. shiny-serverの導入・注意点

---
### 自己紹介

@snap[west span-50]
<img src="oreoreEHR/img/dog_circle.png" height="250" alt= "title"/>  
@snapend

@snap[east span-50]
- @fa[twitter fa-2x twitter-blue] @R_beginner
- 仕事：内科開業医
- 趣味：マラソン、Rで電子カルテをモニョモニョすること、Rの環境構築 
- 好きな開発環境、エディター:Rstudio, neovim
- 好きなRstudioショートカット：shift + alt + 矢印, alt + 矢印
@snapend

+++
### 自己紹介

<div id="column1">
<img src="oreoreEHR/img/dog_circle.png" height="250" alt= "title"/>  
</div>
<div id="column2">
- @fa[twitter fa-2x twitter-blue] @R_beginner
- 職業
- 
</div>


---
### shinyによる電子カルテ補助システム

- 電子カルテに欲しい機能はRでつくる
- shiny-serverにのせて、24時間運用、<br>（Rstudioの邪魔にならない）

+++
### 当院のネットワーク構成

<img src="oreoreEHR/img/Clinic_Network.png" height="500" alt= "title"/>  

+++
### Rで接続

シェーマ 

---
### shiny apps

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
### 患者B「インフルエンザのワクチンは<br>いつ摂取したら良いですか？」

+++
### Flu患者数

<img src="oreoreEHR/img/fluplot.png" height="500" alt= "title"/>  

+++
### 患者C「近くによい皮膚科はありますか？」

+++
### patient map

<img src="oreoreEHR/img/ms_map.jpg" height="500" alt= "title"/>  

+++
### clinic map

<img src="oreoreEHR/img/map_hp.jpg" height="500" alt= "title"/>  

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

```
install script
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

