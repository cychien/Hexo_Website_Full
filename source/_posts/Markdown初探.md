---
title: Markdown初探
date: 2017-06-27 22:32:12
tags:
categories:
- Tool
---
	
資訊世界真的很神奇，在任何角落都可能迸發出新的創意，在任何角落也都有可能遇見他人的創意。

像這次，我因為想利用Evernote記錄下我學程式的一些心得，但卻不滿意Evernote內建的程式碼區塊，且也不太清楚如何使用，而決定到網路上搜尋其他的方案，碰巧發現了**Markdown**這新奇的玩意，在遍覽數個網站後，發現沒有簡易的方法將Evernote調適為適合撰寫程式碼的筆記，加上在無數個網站上都看到**Markdown**這名詞，於是我便下定決心好好了解它一翻。

這一了解下去不得了了，發現這個工具實在是太好玩太方便啦!!

**Markdown**是個輕量級標記性語言，其目標是為了實現**易寫易讀**，利用Markdown寫出的文章比起Html更像是一篇純文章，這個特性對於用來寫筆記而言實在太適合，且他的說明文件並不難，學過Html的應該都十分容易上手，在Markdown中亦可穿插Html，若兩種都會了，就能隨心所欲在不同場合利用較有效率的工具，達到事半功倍啊 ~ ~ 

在說明文件中有一段話，我覺得非常棒，恰恰點出了Markdown的定位

>HTML 是一種發佈的格式

>Markdown是一種編寫的格式

另外我也對創造出Markdown的人深感敬佩，居然能有如此特別的想法!

好! 再來就羅列些Markdown在**易寫易讀**這方面的特色吧!

(完整說明請參閱[說明文件](http://markdown.tw/))

### 特殊字元自動轉型
	
若想在Html中寫Html碼，一些字須做轉換，像 `<` 和 `&` 必須轉成 `&lt;` 和 `&amp;`

例如: 想在網頁上呈現

    <br/> 是換行符號

必須在html裡打成

	&lt;br /&gt; 是換行符號

但在Markdown裡並不需要，只要將它放在程式碼區塊就好，如用以下的寫法

	`<br/>`

轉換後會得到如下的Html碼

	<code> &lt;br /&gt; </code>

達到特殊字元自動轉型的功能  
結果會是 `<br/>` 

### 換行的方便

-   在Markdown裡的換行只須加兩個空白後再加上enter即可，相當於做到了 `<br/>`的效果，若不這樣做，Markdown也會做到自動分行

-   在Markdown裡的分段只須段與段間空一行即可，相當於用了 `<p>` 分別夾住兩個不同的段落  

-   但也有個小問題，那就是無論你段與段間空了幾行，效果依舊和指控一行是一樣的，因此若須空多行，可能得依靠 `<br /> `來解決

### Html語法的簡化
	
在Markdown中你能用更簡潔的語法達到與Html語法有相同功能的效果  

在Html中表示標題位階的 `<h1>` 到 `<h6>` ，在Markdown中可以以如下的方式實現

    # h1  
    ## h2  
    ### h3  

其中一個 `#` 代表的就是第一位階，也就是最大的標題，再來以此類推

在Html中的連結，Markdown用以下的方式做到
	
先假設Html語法 

  	<a href="https://www.google.com.tw/"> Google </a>

-   行內方式

	    [Google](https://www.google.com.tw/)

-   參考方式

		[Google][src]

	在下面加上src代表的URL

	<code>[src]: https://www.google.com.tw/</code>
			
-   簡化方式

	   	[Google][]  
	<code>[Google]: https://www.google.com.tw/</code>  
	(小提醒: 用參考方式或簡化方式時，記得標註網址的那一行之前不可以有空格)

Html中的清單一樣在Markdown中有更簡單的表示法

-   無序

		* xxx  
		* ooo  
		* xox

	其中 `*` 可以 `+` 或 `-` 替代，但記得後面都要加一個空白鍵

-   有序

	    1. xxx  
	    2. ooo  
		3. xox

	蠻直觀的，但後面一樣要有空白鍵

-   巢狀

		1. xxx  
			* ooo  
		2. xxx  
			* ooo  
			* ooo

	差一個階層必須距離一個tab( = 4個空白鍵)

### 程式碼區塊
	
添加程式碼區塊的方便性是我最喜歡Markdown的地方，添加的方式十分簡單

-   單詞

	用 `` ` `` 把單詞夾住，例如 ``  `單詞`  `` ，記得前後都要多加上一個空白鍵

-   一段程式碼

    先空一行，再將下一行開頭空出一個tab後再開始寫，如果程式碼想寫在清單中，則每多一個階級必須多空一個tab

    雖然看似蠻複雜的，但熟悉之後就會覺得這項語法實在是太方便啦!!

    如果看的不是很懂，這裡有一篇[文章][codeInList]專門在講清單裡的程式碼區塊究竟該如何插入
      
[codeInList]:http://meta.stackexchange.com/questions/3792/how-to-nest-code-within-a-list-using-markdown

<br />
好啦! 這就是對Markdown的小小簡單介紹，如果還有興趣，歡迎再更深入的了解哦

話說這是我第一篇用Markdown寫出的文章哩，真的覺得好好玩 ^_^

<br />
參考資料:
1. https://zh.wikipedia.org/wiki/Markdown
2. http://markdown.tw/
3. http://meta.stackexchange.com/questions/3792/how-to-nest-code-within-a-list-using-markdown




