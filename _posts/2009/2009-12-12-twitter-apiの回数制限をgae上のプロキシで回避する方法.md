---
title: Twitter APIの回数制限をGAE上のプロキシで回避する方法
author: azu
layout: post
permalink: /2009/1212/res1508/
aktt_notify_twitter:
  - no
SBM_count:
  - '00127<>1355440976<>118<>0<>8<>1<>0'
dsq_thread_id:
  - 300802496
categories:
  - インストール設定
tags:
  - API
  - google
  - twitter
  - 設定
---
Twitter API proxyをGoogle App Engine上で動かす方法

### プロキシの必要性

プロキシを経由してAPIにアクセスすると何がいいのか

*   TwitterのAPIはアカウント&IPアドレスでカウントされているので、APIの使用回数を増やせる。  
    グローバルIPアドレスをPCごとに変えられない環境で2台のPCから見るときに便利
*   Twitterへのアクセスが制限されている環境でも使用できる。  
    元々GFW（グレート・ファイアー・ウォール）を超えるために作られたっぽい
*   ちゃんと確認してないけど、GAEはホワイトリストに入ってる気がするので、API回数が20000になったりする事もある。

要はAPI使用回数を増やせるよって話。

### 必要なもの

*   <a href="https://appengine.google.com/" target="_blank">Google App Engine</a>のアカウント
*   [Google App Engine SDK][1] と[Python][2]実行環境
*   [birdnest ][3] 一式のファイル(Python)

まずは<a href="https://appengine.google.com/" target="_blank">Google App Engine</a>のアカウントを作成します。(すでに持ってる人は、新しいアプリケーションを作成するだけ)  
[Google App Engineのアカウント登録とアプリ作成 &#8211; 130単位][4] を参考にアカウントと<span>アプリケーション</span>を作成します。  
このときのアプリケーションのIDは後で必要になるので控えといて下さい(Google App Engineのページにアクセスすればいつでも見られるけど)

次に[開発環境 &#8211; Google App Engine &#8211; Google Code][5]を参考にPythonの実行環境とSDKをダウンロード&インストールします。(ここも既にやってる人はスルー)  
Pythonは[2.5][6]が推奨されているのでできればそちらをダウンロードした方がいいです。(一応2.6でも動きました)

次に今回、Google App Engine上で動かすプロキシのスクリプトである、[birdnest][3]をダウンロードします。  
http://birdnest.googlecode.com/svn/branches/gae/ 以下のファイルを全部ダウンロードするのですが、そのままだと面倒なので、  
[【第１７回】EclipseでSubversion &#8211; Tech [Friday] プロモバイルエンジニアブログ][7] などを参考にEclipseを使ってSVNをチェックアウトしてまとめてダウンロードしました。

<br class="spacer_" /><figure id="attachment_1510" style="width: 213px;" class="wp-caption alignnone">

[<img class="size-full wp-image-1510" title="sshot-2009-12-12-1" src="https://efcl.info/wp-content/uploads/2009/12/sshot-2009-12-12-1.png" alt="ダウンロードしたファイル" width="213" height="169" />][8]<figcaption class="wp-caption-text">ダウンロードしたファイル</figcaption></figure> 
これらのファイルを先ほど作成した**アプリケーションID名のフォルダ**を作りその中に入れておきます。(これは説明を統一するためなので絶対必要な操作ではないかも)  
アプリケーションのIDがtwitproxyならtwitproxyというフォルダを作りその中に入れる。

### ファイルの編集

ダウンロードしたapp.yamlを編集する必要があります。  
一行目のアプリケーション名を先ほど控えてた自分が作成したGoogle App EngineのアプリケーションIDに変更します。

<pre>application: アプリケーションのID</pre>

### テスト&アップロード

Pythonが入っていれば、コマンドプロンプトでPythonと打ち込むとバージョンが表示されます。(エラーになる場合はPython周りがおかしい)  
まずは動くかどうかをテストします。  
GAEではローカルでテストするためにSDKにサーバがあるのでそのスクリプトを起動して確認します。  
同じくコマンドプロンプト上で、

<pre>Python "C:Program FilesGooglegoogle_appenginedev_appserver.py" 先ほど作成したフォルダへのパス
</pre>

と打ち込んだ状態(コマンドプロンプトは閉じない)でhttp://localhost:8080/にアクセスとページが表示されると思います。  
この上でTwitterクライアントのAPIURLをhttp://localhost:8080/api/ にして使えるか確認できるといいです。  
[Google App Engineを使ったTwitterBotの作り方 &#8211; 星屑日記][9] を詳しく読むといいかも。

#### アップロード

次に編集したファイルをGAEにアップロードします。  
先ほど同様にコマンドプロンプト上から

<pre>Python "C:Program FilesGooglegoogle_appengineappcfg.py update 先ほど作成したフォルダへのパス
</pre>

と打ち込むとアップロードが始まります。

そしてhttp://アプリケーション名.appspot.com/にアクセスしてテストの時と同じページが表示されればアップロードは成功です。

### 実際にプロキシとして使用する

*   [Echofon for Twitter (Formerly TwitterFox)][10]

Echofonをインストールしてextensionフォルダに行きます

vista

<pre>C:Usersユーザー名AppDataRoamingMozillaFirefoxProfileseプロファイル名extensionstwitternotifier@naan.netcomponents

</pre>

XP

<pre>C:Documents and Settingsユーザー名Application DataMozillaFirefoxProfilesプロファイル名extensionstwitternotifier@naan.netcomponents
</pre>

ここにあるnsTwitterFox.jsを編集します。

var TWITTER\_API\_URL = &#8220;http://アプリケーション名.appspot.com/api/&#8221;;

と変更すると、GAE上で動いているプロキシを通してAPIにアクセスすることができます。  
基本的にAPIのURL指定を変更できるソフトなら同じ方法でプロキシを通してアクセスできる。

海外のクライアントだとAPIを指定できるソフトも多いです。

[Spaz][11]の場合だと設定画面にBase API URLを指定できるので、同じようにhttp://アプリケーション名.appspot.com/api/を入れればプロキシ経由で使用できます。  
[<img class="alignnone size-medium wp-image-1512" title="sshot-2009-12-12-2" src="https://efcl.info/wp-content/uploads/2009/12/sshot-2009-12-12-2-300x182.png" alt="sshot-2009-12-12-2" width="300" height="182" />][12]

<br class="spacer_" />

設置が凄い面倒でセキュリティを気にしない人は

[birdnest &#8211; a twitter api proxy][13]を同じように使えるかもね。

以下参考

**birdnest &#8211; Project Hosting on Google Code**
:   [http://code.google.com/p/birdnest/][14]

**How to set up Twitter API Proxy with Google App Engine | Bruce Yo&#8217;s Blog**
:   [http://www.bruceyo.info/archives/402][15]

**Google App Engineを使ったTwitterBotの作り方 &#8211; 星屑日記**
:   [http://d.hatena.ne.jp/intheflight/20090611/p1][16]

<div id="_mcePaste" style="overflow: hidden; position: absolute; left: -10000px; top: 0px; width: 1px; height: 1px;">
  GFW（グレート・ファイアー・ウォール）
</div>

 [1]: http://code.google.com/intl/ja/appengine/downloads.html
 [2]: http://www.python.org/
 [3]: http://code.google.com/p/birdnest/
 [4]: http://d.hatena.ne.jp/deeeki/20091201/gae_register_account
 [5]: http://code.google.com/intl/ja/appengine/docs/python/gettingstarted/devenvironment.html
 [6]: http://www.python.jp/Zope/download/pythoncore
 [7]: http://blog.promob.jp/fri/2009/02/eclipsesubversion.html
 [8]: https://efcl.info/wp-content/uploads/2009/12/sshot-2009-12-12-1.png
 [9]: http://d.hatena.ne.jp/intheflight/20090611/p1
 [10]: https://addons.mozilla.org/ja/firefox/addon/5081
 [11]: http://getspaz.com/
 [12]: https://efcl.info/wp-content/uploads/2009/12/sshot-2009-12-12-2.png
 [13]: http://nest.appspot.com/
 [14]: http://code.google.com/p/birdnest/ "birdnest - Project Hosting on Google Code"
 [15]: http://www.bruceyo.info/archives/402 "How to set up Twitter API Proxy with Google App Engine | Bruce Yo's Blog"
 [16]: http://d.hatena.ne.jp/intheflight/20090611/p1 "Google App Engineを使ったTwitterBotの作り方 - 星屑日記"
