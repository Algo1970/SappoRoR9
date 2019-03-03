### <font id='p_title'>Rによる電子カルテ補助システム</font>

<font id='p_main'>~ shiny-server導入編 ~</font><br><br>
<font id='p_small_gray'>  </font>

---
### <font id='p_title'>本日の内容</font>

1. <font id='p_title'>自己紹介</font>
2. <font id='p_title'>Rで電子カルテへ接続</font>
3. <font id='p_title'>当院でのshiny活用例</font>
4. <font id='p_title'>shiny-serverの導入と注意点</font>



---
### <font id='p_title'>自己紹介</font>

<div id="column300">
<img src="oreoreEHR/img/dog_circle.png" height="250" alt= "title"/>  
</div>
<div id="column660">
@fa[twitter fa-1x twitter-blue] <font id='p_main'>：@R_beginner</font> <br>
<font id='p_main'>仕事：内科開業医(消化器病、肝臓病) </font><br>
<font id='p_main'>趣味：マラソン, R, <br>　　　pythonでドローン遊び<br>　　　neovimの設定ファイルいじり</font><br>

</div>

---
### <font id='p_title'>当院電子カルテ</font>

<img src="oreoreEHR/img/EHR2.jpg" height="500" alt= "title"/> 

+++
### <font id='p_title'>当院電子カルテの問題点</font>

1.  <font id='p_title'>患者ID毎に経時的な情報しか表示できない</font>
2.  <font id='p_title'>検索機能が貧弱</font>
3.  <font id='p_title'>各種診断スコア等の計算ができない</font>
4.  <font id='p_title'>再診予約プログラムが使いにくい</font>
5.  <font id='p_title'>地図を表示できない　等</font>

+++
### <font id='p_title'>困った事はRで解決しましょう</font>

+++
- <font id='p_title'>電子カルテの追加機能をshinyで作成</font>
- <font id='p_title'>ついでにshiny-serverにのせて、24時間運用</font>

+++
### <font id='p_title'>当院のネットワーク構成</font>

<img src="oreoreEHR/img/Clinic_Network2.png" height="500" alt= "title"/>  


+++
### <font id='p_title'>Rで接続</font>

<img src="oreoreEHR/img/RODBC.png" height="300" alt= "title"/>  

+++
### <font id='p_title'>電子カルテDB内のテーブル</font>

- <font id='p_title'>患者マスター</font>
- <font id='p_title'>患者住所テーブル</font>
- <font id='p_title'>病名テーブル</font>
- <font id='p_title'>採血データテーブル</font>
- <font id='p_title'>カルテ所見テーブル</font>
- <font id='p_title'>処方・処置テーブル　等</font>

+++
### <font id='p_title'>データの抽出</font>

- <font id='p_title'>データ抽出＋前処理用の参照クラス</font>

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
### <font id='p_title'>shinyアプリの紹介</font>

<font id='p_main'>イメージしやすいように、診察室の風景風に…</font>

---
### <font id='p_title'>Hクリニック 第一診察室 </font>

+++
### <font id='p_title'>症例 :　７０代男性、<br>糖尿病、アルコール性肝硬変 </font>

+++
- <font id='p_main'>医師『食事療法はうまくいってますか？』</font>
- <font id='p_main'>患者『頑張っていますが…』</font>
- <font id='p_main'>医師『体重は減ってきましたか？』</font>
- <font id='p_main'>患者『うっ………』</font>

+++
### <font id='p_title'>体重経過と必要栄養量</font>

<img src="oreoreEHR/img/obesity.jpg" height="500" alt= "title"/>  

+++
- <font id='p_main'>患者『薬が効いてないんじゃないの？(キレ気味)』</font>

<img src="oreoreEHR/img/doctor_isee2.png" height="200" alt= "title"/>  

+++
### <font id='p_title'>推定糸球体濾過量プロット</font>
<img src="oreoreEHR/img/eGFRplot.png" height="400" alt= "title"/>  
<font id='p_main'>腎予後の改善を可視化</font>

+++
- <font id='p_main'>患者：肝臓はどうでしょうか…(反省)</font>
- <font id='p_main'>医師：まだ大丈夫ですが…</font>  
  
<img src="oreoreEHR/img/doctor_question.png" height="200" alt= "title"/> 

+++
<img src="oreoreEHR/img/predict_LT.png" height="300" alt= "title"/>  

+++
- <font id='p_main'>患者『運動やってみようかな…』</font>
- <font id='p_main'>医師『市民マラソンとか楽しいですよ。<br>　　　待合にRunnersって本置いてありますから…<br>　　　RNNNETで探して…』</font>

+++
### <font id='p_main'>患者と世間話で盛り上がり、<br>待合が混んできても気づけず<br>スタッフがイライラ…</font>
<img src="oreoreEHR/img/angry_nurse.png" height="200" alt= "title"/>  

+++
### <font id='p_title'>診察待ち患者アラート</font>
<img src="oreoreEHR/img/alert.png" height="200" alt= "title"/>  
<font id='p_main'>なるべく患者に気付かれないように<br>アラートをチラ見</font>

+++
- <font id='p_main'>医師『次回受診までに運動療法取り入れてみましょうね』</font>
- <font id='p_main'>患者『頑張ってみます』</font>
- <font id='p_main'>医師『次回の予約ですが…』</font>

+++
### <font id='p_title'>再診日、時間が決まらない</font>
<img src="oreoreEHR/img/reservation_man.png" height="200" alt= "title"/> 

+++
<img src="oreoreEHR/img/shiny_yoyaku.gif" height="600" alt= "title"/>  

---
### <font id='p_title'>Hクリニック 第二診察室 </font>

+++
- <font id='p_main'>患者『この間インフルエンザになっちゃって…<br>
  来年はワクチン打ちたいんだけど、いつ頃がよいですか？』</font><br>
<img src="oreoreEHR/img/confused_grandma.png" height="200" alt= "title"/>  

- <font id='p_main'>医師『ここ最近のインフルエンザ流行期は…』</font>

+++
### <font id='p_title'>インフルエンザ患者数</font>

<img src="oreoreEHR/img/fluplot.png" height="500" alt= "title"/>  

+++

- <font id='p_main'>患者『テレビでC型肝炎が飲み薬で治るって言ってたけど…』</font>
- <font id='p_main'>医師『今は、ほとんどの人が完治します。<br>こちらを御覧ください…』</font>

+++?image=oreoreEHR/img/Rmd_HCV.png&size=contain



---
### <font id='p_title'>ほぼ全ての電子カルテデータは<br>利用できたが…</font>

+++
### <font id='p_title'>患者位置情報だけは、<br>利用できていません</font>

<font id='p_main'>インターネットに繋げないから…</font>


+++
<img src="oreoreEHR/img/OpenMapTile.jpg" height="400" alt= "title"/> 
<font id='p_title'>無いものは作ろう院内地図サーバー</font>
<p id="p_small_gray">https://openmaptiles.com/</p>


+++
### <font id='p_title'>Dockerコンテナを利用</font>

地図データダウンロード後、docker run
```
wget -c https://openmaptiles.com/download/WyJhMjRlMmNhNi04OGQ2LTQ3NGMtOTg0Yi1jZDRjZGMxZGJkNTgiLCItMSIsODkyMV0.Dw2_Ig.v1BUsFqm5XhXAkHRDI6KKXTEYtw/osm-2017-07-03-v3.6.1-asia_japan.mbtiles?usage=education
sudo docker run --rm -it -v $(pwd):/data -p 8080:80 klokantech/openmaptiles-server
```


+++
### <font id='p_title'>leafletにaddTilesで追加</font>

```
leaflet::leaflet() %>% 
  addTiles( urlTemplate = "http://192.168.XX.XXX:8080/styles/osm-bright/{z}/{x}/{y}.png") %>%
  setView(141.336, 43.118 , zoom = 9) %>% 
  addMarkers(141.5233148, 42.6186386 )
```

+++
### <fong id='p_title'>連携医療機関map</font>

<img src="oreoreEHR/img/hp_map.jpg" height="550" alt= "title"/>  



+++
### <fong id='p_title'>インフルエンザ感染者MAP</font>

<img src="oreoreEHR/img/kitaku_flu.png" height="500" alt= "title"/>  

---
### <fong id='p_title'>SappoRoR#5からの変更点</font>

- 自前MapServerで位置情報の可視化
- DB接続、テーブルごとの前処理までを<br>Rの参照クラスで書き換え
- shiny用snippet作成で、開発効率アップ


---
### <font id='p_title'>最後にshinyの話</font>

---
### <font id='p_title'>質問</font>

<font id='p_main'>1. shiny appを使ったことありますか？</font><br>
<font id='p_main'>2. shiny-serverをインストールしたことありますか？</font><br>
<font id='p_main'>3. shiny-serverに自作アプリを入れたことありますか？</font>

---
### <font id='p_title'>shiny appsの作り方</font>

<img src="oreoreEHR/img/shiny_site_book.png" height="500" alt= "title"/>  

---
### <font id='p_title'>shiny severの導入</font>

+++
### <font id='p_title'>対応OS</font>

<img src="oreoreEHR/img/server_os2.png" height="400" alt= "title"/>  

+++
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

<img src="oreoreEHR/img/shiny_libpath.jpg" height="300" alt= "title"/>  

<font id='p_title_orange'>IPaddresss:3838/sample-apps/フォルダ名</font><br>
<font id='p_main'>にブラウザでアクセスするだけ。</font>

+++
### <font id='p_title'>shiny-serverの注意点</font>

1. <font id='p_title'>sample-appsのアクセス権限</font><br>
2. <font id='p_title'>library pathの問題</font>

+++?image=oreoreEHR/img/permission.gif&size=contain

+++
### <font id='p_title'>ファイルの権限</font>

<img src="oreoreEHR/img/chmod.png" height="209" alt= "title"/>  

+++
### <font id='p_title'>サンプルアプリ</font>

<img src="oreoreEHR/img/ggplot_test.png" height="500" alt= "title"/>  

+++
### <font id='p_title'>Disconnected from the server</font>

<img src="oreoreEHR/img/shiny_error.png" height="400" alt= "title"/> 

+++
### <font id='p_title'>library path</font>

<img src="oreoreEHR/img/libpaths.png" height="503" alt= "title"/>  

+++
### <font id='p_title'>.libPaths()を表示するshinyアプリ</font>

<img src="oreoreEHR/img/libpath.png" height="500" alt= "title"/>  

+++
<img src="oreoreEHR/img/shinyserver_libpath.png" height="500" alt= "title"/>  

---
### <font id='p_title'> まとめ　</font>

<font id='p_title'>sample-appsのアクセス権限変更を</font><br>

```
$ sudo chmod -R 777 /srv/shiny-server/sample-apps
```

<font id='p_title'>library pathの違いに気をつける</font><br>

```
$ sudo chmod -R 777 /usr/local/lib/R/site-library
```

```
> install.packages("shinythemes", lib = .libPaths()[2])
```










