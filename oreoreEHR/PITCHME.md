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

- @fa[twitter fa-2x twitter-blue] @R_beginner


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
### 診察待ち患者アラート

<img src="oreoreEHR/img/alert.png" height="250" alt= "title"/>  

+++
### 体重経過と必要栄養量

<img src="oreoreEHR/img/BWplot.png" height="500" alt= "title"/>  

+++
### eGFRプロット

<img src="oreoreEHR/img/eGFRplot.png" height="500" alt= "title"/>  

+++
### Flu患者数

<img src="oreoreEHR/img/fluplot.png" height="500" alt= "title"/>  


+++
### patient map

<img src="oreoreEHR/img/ms_map.jpg" height="500" alt= "title"/>  

+++
### clinic map

<img src="oreoreEHR/img/map_hp.jpg" height="500" alt= "title"/>  

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
### pyramid

<img src="oreoreEHR/img/pyramid.jpg" height="500" alt= "title"/>  

+++
### pyramid DM

<img src="oreoreEHR/img/pyramid_DM.jpg" height="500" alt= "title"/>  

+++?image=oreoreEHR/img/Rmd_HCV.png&size=contain
### Rmarkdown

<img src="oreoreEHR/img/Rmd_HCV.png" height="500" alt= "title"/>  


+++?image=oreoreEHR/img/Rmd_HCV.png&size=cover
### Rmarkdown

+++?image=oreoreEHR/img/Rmd_HCV.png&size=auto 90%
### Rmarkdown

+++?image=oreoreEHR/img/Rmd_HEV.png&size=contain
### Rmarkdowm2



+++
### yoyaku

<img src="oreoreEHR/img/yoyaku.png" height="500" alt= "title"/>  






---
### 最近の変更点

- DB接続、テーブルごとの前処理までをRの参照クラスで書き換え
- shiny用snippet作成で、開発効率アップ

+++
### 参照クラスの例

```
refclass
```

+++
### shiny用スニペットの例

```
snippet
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


+++
### <i class="em em-gift">test</i>

@snap[west span-50]
<img src="oreoreEHR/img/ms_yoyaku.gif" height="500" alt= "title"/>  
@snapend

@snap[east span-50]
<img src="oreoreEHR/img/shiny_yoyaku.gif" height="500" alt= "title"/>  
@snapend

+++
### 予約システム

@snap[west span-50]
<img src="oreoreEHR/img/ms_yoyaku.gif" height="500" alt= "title"/>  
@snapend

@snap[east span-50]
<img src="oreoreEHR/img/shiny_yoyaku2.gif" height="500" alt= "title"/>  
@snapend






+++ 
### 

<div id="column1">
<img src="oreoreEHR/img/ms_yoyaku.gif" height="500" alt= "title"/>   
</div>
<div id="column2">
<img src="oreoreEHR/img/shiny_yoyaku.gif" height="500" alt= "title"/>  
</div>










---?color=#6b8e23
### <font id='p_120_normal'>1980</font><font id='p_normal'>年に</font><font id='p_120_normal'>Ludwig</font><font id='p_normal'>が</font><font id='p_120_normal'>NASH</font><font id='p_normal'>を提唱し</font><br><font id='p_120_normal'>40</font><font id='p_normal'>年近く経ちました.<br>私達、肝臓内科医が学んだことは…</font>




+++?color=#6b8e23
### まとめ

