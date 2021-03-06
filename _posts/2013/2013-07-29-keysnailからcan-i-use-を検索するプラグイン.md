---
title: 'KeySnailからCan I use&#8230;を検索するプラグイン'
author: azu
layout: post
permalink: /2013/0729/res3362/
dsq_thread_id:
  - 1544193127
categories:
  - Firefox
  - KeySnail
tags:
  - browser
  - Firefox
  - KeySnail
  - 検索
---
タイトルどおりですが、[Can I use&#8230;][1] をKeySnailのプロンプトから検索するプラグインを書きました

<img src="https://efcl.info/wp-content/uploads/2013/07/CanIuse.png" alt="NewImage" title="CanIuse.png" border="0" width="600" height="123" />

*   [KeySnail-Plugins/caniuse-keysnail at master · azu/KeySnail-Plugins][2]
*   <https://raw.github.com/azu/KeySnail-Plugins/master/caniuse-keysnail/caniuse-keysnail.ks.js> からインストールできると思います

## 動画



KeySnailのプロンプトから、検索して該当する項目の [Can I use&#8230;][1] のページを開きます。

## 背景

Can I use…に行って検索するのが面倒だったので書いた。

[Can I use&#8230;][1] は現在Githubにリポジトリを持っていて、  
サイトで公開されているデータに対してPull Requestなどでコントリビュートすることができます。

*   [Fyrd/caniuse][3]
*   [Data contributions via GitHub][4]

## 仕組み

Githubのリポジトリでは[data.json][5]というサイトの元?になってるデータのファイルがJSONで公開されているので、  
プラグインでは毎回これを読みに行って検索候補として利用しています。

[Addon-SDKでFirefoxのアドオンを書くための開発環境を作る | Web scratch][6] で書いたように、Firefox21からAddon SDKのAPIがFirefoxにバンドルされているので、  
[request][7] を使って書いていましたが、  
Nightlyでの動きがおかしかったので普通に[XHR][8]を使うようにしました。

Firefoxで `resource://gre/modules/commonjs/sdk/` にアクセスするとバンドルされてるAddon SDKの中身が見られます。  
これは、[Add-on SDK][9]がほぼそのまま入ってるみたいで、  
AddonSDKに依存した感じのモジュール(又、それを利用するモジュール)は動作してない感じなってたりするのが残念。

*   [KeySnail-Plugins/caniuse-keysnail at master · azu/KeySnail-Plugins][2]

 [1]: http://caniuse.com/ "Can I use... Support tables for HTML5, CSS3, etc"
 [2]: https://github.com/azu/KeySnail-Plugins/tree/master/caniuse-keysnail "KeySnail-Plugins/caniuse-keysnail at master · azu/KeySnail-Plugins"
 [3]: https://github.com/Fyrd/caniuse "Fyrd/caniuse"
 [4]: http://caniuse.com/feed.php?id=136 "Data contributions via GitHub"
 [5]: https://github.com/Fyrd/caniuse "data.json"
 [6]: https://efcl.info/2013/0721/res3346/ "Addon-SDKでFirefoxのアドオンを書くための開発環境を作る | Web scratch"
 [7]: https://addons.mozilla.org/en-US/developers/docs/sdk/latest/modules/sdk/request.html "request"
 [8]: https://github.com/azu/KeySnail-Plugins/commit/866ccaf177113ca57f8c363163e3d4d4ab1e3bb3 "XHR"
 [9]: https://addons.mozilla.org/en-US/developers/docs/sdk/latest/dev-guide/tutorials/installation.html "Add-on SDK"
