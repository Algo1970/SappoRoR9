### <img src="oreoreEHR/img/oreoreEHR.png" height="600" alt= "title"/>  


---
### <font id='p_title'>本日の内容</font>

1. <font id='p_title'>自己紹介</font>
2. <font id='p_title'>電子カルテ補助システムについて</font>
3. <font id='p_title'>shiny-serverの導入・注意点</font>
4. <font id='p_title'>実際のshinyアプリ</font>
5. <font id='p_title'>ここ1年の変更点</font>




---
### <font id='p_title'>自己紹介</font>

<div id="column300">
<img src="oreoreEHR/img/dog_circle.png" height="250" alt= "title"/>  
</div>
<div id="column660">
@fa[twitter fa-1x twitter-blue] <font id='p_main'>@R_beginner</font> <br>
<font id='p_main'>仕事：内科開業医 </font><br>
<font id='p_main'>趣味：マラソン、R、<br>電子カルテハッキング </font><br>
<font id='p_main'>好きな開発環境:Rstudio, neovim </font><br>
<font id='p_main'>好きなRstudio shortcut：alt + 矢印</font><br>
</div>

---
### <font id='p_title'>電子カルテ補助システムについて</font>

電子カルテの写真？

+++
### <font id='p_title'>当院電子カルテの問題点</font>

1.  <font id='p_title'>患者ID毎に経時的な情報しか表示できない</font>
2.  <font id='p_title'>検索機能が貧弱</font>
3.  <font id='p_title'>各種診断スコア等の計算ができない</font>
4.  <font id='p_title'>次回予約プログラムが使いにくい</font>
5.  <font id='p_title'>ローカルネットワークのため、<br>地図を表示できない</font>
<br><br><font id='p_title'>等</font>

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
### <font id='p_title'>shiny severのinstall</font>

1. <font id='p_title'>R, Rstudioのインストール</font><br>
2. <font id='p_title'>shinyパッケージのインストール</font><br>
3. <font id='p_title_orange'>shiny-serverのインストール </font><br>


```
sudo apt-get install gdebi-core
wget https://download3.rstudio.org/ubuntu-14.04/x86_64/shiny-server-1.5.9.923-amd64.deb
sudo gdebi shiny-server-1.5.9.923-amd64.deb
```

+++?image=oreoreEHR/img/install_shiny.png&size=contain


+++
### <font id='p_title'>shinyアプリの保存先</font>

<img src="oreoreEHR/img/sample_folder.jpg" height="400" alt= "title"/>  

+++
### <font id='p_title'>アプリに接続</font>

<img src="oreoreEHR/img/shiny_libpath.jpg" height="500" alt= "title"/>  

<font id='p_title_orange'>IPaddresss:3838/sample-apps/フォルダ名</font><br>
<font id='p_main'>にブラウザでアクセスするだけ。</font>

+++
### <font id='p_title'>shiny-serverの注意点</font>

1. <font id='p_title'>sample-appsのアクセス権限</font><br>
2. <font id='p_title'>libpathの違い</font>

+++?image=oreoreEHR/img/chmod.png&size=contain




---
### <font id='p_title'>当院で使用中のshinyアプリ</font>

+++
### <font id='p_title'>画面いっぱいにカルテ表示すると待ち患者が見えない…</font>

+++
### <font id='p_title'>診察待ち患者アラート</font>

<img src="oreoreEHR/img/alert.png" height="250" alt= "title"/>  

+++
### <font id='p_title'>長期処方で予約日が決めにくい</font>

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
### <font id='p_title'>体重の経過が見にくい</font>
### <font id='p_title'>必要栄養量の計算が面倒</font>

+++
### <font id='p_title'>体重経過と必要栄養量</font>

<img src="oreoreEHR/img/BWplot.png" height="500" alt= "title"/>  

+++
### <font id='p_title'>糖尿病患者A「薬を辞めてもいいですか？」</font>

+++
### <font id='p_title'>eGFRプロット</font>

<img src="oreoreEHR/img/eGFRplot.png" height="500" alt= "title"/>  

+++
### <font id='p_title'>患者B「インフルエンザのワクチンは<br>いつ接種したら良いですか？」</font>

+++
### <font id='p_title'>インフルエンザ患者数</font>

<img src="oreoreEHR/img/fluplot.png" height="500" alt= "title"/>  

+++
### <font id='p_title'>患者D「私の肝臓は大丈夫でしょうか？」</font>

+++
### <font id='p_title'>mayoPBCmodel</font>

<img src="oreoreEHR/img/mayoPBCmodel.png" height="300" alt= "title"/>  

+++
### <font id='p_title'>MELD</font>

<img src="oreoreEHR/img/MELD.png" height="300" alt= "title"/>  

+++
### <font id='p_title'>predict Liver Transplant</font>

<img src="oreoreEHR/img/predict_LT.png" height="300" alt= "title"/>  

+++
### <font id='p_title'>会計士「新患が減っていますが、10年後は大丈夫ですか？」</font>

+++
### <font id='p_title'>人口ピラミッド</font>

<img src="oreoreEHR/img/pyramid.jpg" height="500" alt= "title"/>  

+++
### <font id='p_title'>人口ピラミッド（糖尿病患者）</font>

<img src="oreoreEHR/img/pyramid_DM.jpg" height="500" alt= "title"/>  

+++
### <font id='p_title'>患者G「C型肝炎の薬は、どれぐらい効くの？」</font>
### <font id='p_title'>患者H「C型肝炎の薬は、どう違うのですか？」</font>

+++
### <font id='p_title'>Rmarkdownで患者説明資料</font>

+++?image=oreoreEHR/img/Rmd_HCV.png&size=contain


+++
### <font id='p_title'>患者G「どうして豚肉は生で食べちゃいけないの？」</font>

+++?image=oreoreEHR/img/Rmd_HEV.png&size=contain

---
### <font id='p_title'>患者住所テーブル</font>

- local networkのため、地図表示ができず、患者位置情報を利用できない。

-> 自前MAP server

+++
### <font id='p_title'>自前マップサーバーを立てよう</font>

<img src="oreoreEHR/img/OpenMapTile.jpg" height="500" alt= "title"/> 

<p id="p_small_gray">https://openmaptiles.com/</p>

+++
### <fong id='p_bold_olive'>patient map</font>

<img src="oreoreEHR/img/ms_map.jpg" height="500" alt= "title"/>  

+++
### <fong id='p_bold_olive'>clinic map</font>

<img src="oreoreEHR/img/map_hp.jpg" height="500" alt= "title"/>  






---
### <fong id='p_bold_olive'>SappoRoR#5からの変更点</font>

- DB接続、テーブルごとの前処理までをRの参照クラスで書き換え
- shiny用snippet作成で、開発効率アップ
- 自前MapServerで位置情報の可視化









+++?color=#6b8e23
### まとめ

@snap[east span-60]
@quote[薬は効いているんですか？](患者A)
@snapend

---
### その他

- リモートログオンクライアントで、画像サーバー、駐車場モニター、Rstudioサーバーの管理


+++
### <font id='p_title'>shiny-serverの注意点</font>

1. <font id='p_title'>sample-appsのアクセス権限</font><br>
<font id='p_main'>　sudo chmod -R 777 hoghoge</font>
2. <font id='p_title'>環境変数、スコープ</font>
3. <font id='p_title'>libpathの違い</font>
4. <font id='p_title'>マルチプロセスは、他サイトを参照</font>




