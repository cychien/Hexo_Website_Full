---
title: cookie-parser中間件深入理解
author: Justin
cover: /images/joe-green-475962.jpg
categories: 
- Programming
date: 2017-12-18 12:47:07
---

之前在理解cookie-parser運作原理的時候，看了網路上許多文章，但心中仍然有一些疑問未解，於是這次便鼓起勇氣直接看[原始碼](https://github.com/expressjs/cookie-parser/blob/master/index.js)，看完後發現真的理解不少，而且充滿成就感^_^

cookie-parser是express在做cookie處理不可或缺的一個中間件，他的原始碼並不多，主檔只有180行程式，底下會深入的來看一下裡頭的內容，希望能在二十分鐘內讓大家懂cookie-parser的運作原理

## 終極目標

cookie-parser的終極目標只有一個，那就是
> 將cookie轉換為物件型態，並將一般的cookie放到 `req.cookies` 裡，將有加密的cookie放到 `req.signedCookies` 裡

## 先來認識cookie的種類

1.  一般cookie
    ```javascript
    // 設置
    res.cookie('name', 'John Lee')
    // 在客戶端瀏覽器顯示，字串只會經過URL Encode
    name=John%20Lee
    ```

2.  存物件的cookie
    ```javascript
    // 設置
    res.cookie('name', {firstName: 'John'})
    // 在客戶端瀏覽器顯示，字串不只會經過URL Encode，開頭還會補上'j:'，用來區別這串東西其實是物件，不是簡單的string，cookie-parser會針對有這種前綴的cookie進行特別的剖析
    name=j%3A%7BfirstName%3A%27John%27%7D
    ```

3.  signedCookie
    
    signedCookie指的是有經過密文加密過的cookie
    ```javascript
    // 設定密文
    app.use(reruire('cookie-parser')('secret'))
    // 設置
    res.cookie('name', 'John Lee', {signed: true})
    // 在客戶端瀏覽器顯示，字串不只會經過URL Encode，開頭還會補上's:'，用來區別這串東西其實是signedCookie，後面的x代表內容與密文加密過後的字串
    name=s%3AJohn%20Lee.xxxxxxxxxxxxxxxxxxxxxxxx
    ```

## 引入的library

在cookie-parser的開頭處，你會看到
```javascript
var cookie = require('cookie')
var signature = require('cookie-signature')
```

其中[cookie](https://www.npmjs.com/package/cookie)模組裡的parse方法能讓我們能把cookie字串轉成物件，就像底下的範例
```javascript
var cookies = cookie.parse('name=John');
// cookies = {name: 'John'};
```

[cookie-signature](https://www.npmjs.com/package/cookie-signature)主要是對signedCookies做unsign的動作，如果內容被更改，就會回傳false

## 主函式

```javascript
module.exports = cookieParser
```

主函式是cookieParser，cookieParser的內容如下:

```javascript
// 傳入參數密文(secret)與設定項(options)
function cookieParser (secret, options) {
  
  // 返回的是一個function
  return function cookieParser (req, res, next) {

    // 如果已經有req.cookies就跳過，不然會把舊值覆蓋掉
    if (req.cookies) {
      return next()
    }

    // 取得一長串的cookie，內容像是: name1=John%20Lee;name2=s%3AJohn%20Lee.xxxxxxxxxxxxxxxxxxxxxxxx
    var cookies = req.headers.cookie

    // 如果secret=undefined，secrets=[] ; 如果secret是一個多組密文組成的陣列，secrets = secret
    var secrets = !secret || Array.isArray(secret)
      ? (secret || [])
      : [secret]

    // 訂定第一個密文(如果有很多的話)做加密時的密文，在res.cookie('', '', { signed: true })會用到這個密文做加密
    req.secret = secrets[0]
    // 創立兩個空物件來存放解析後的cookie物件
    req.cookies = Object.create(null)
    req.signedCookies = Object.create(null)

    // 沒有cookies當然就不用處理啦~
    if (!cookies) {
      return next()
    }

    // 調用上面說過的cookie.parse()，將cookie從字串，轉為物件
    req.cookies = cookie.parse(cookies, options)

    // 先處裡signedCookie(其實就是驗證內容有無被更改)，再處理內容是物件的cookie
    if (secrets.length !== 0) {
      // signedCookies()方法在主函式下面
      req.signedCookies = signedCookies(req.cookies, secrets)
      // JSONCookies()方法在主函式下面
      req.signedCookies = JSONCookies(req.signedCookies)
    }

    // 處理內容是物件的cookie
    req.cookies = JSONCookies(req.cookies)

    next()
  }
}
```

## 其他函式

在主函式裡調用的其他函式，像signedCookies()、JSONCookies()其實都被定義在主函式的下面，這邊就不一個個來細看，大致說明一下每個函式的用途就好，有興趣的可以去看原始碼，寫得很簡單易懂

這邊定義四種函式 `signedCookies()`、`signedCookie()`、`JSONCookies()`、`JSONCookie()`

可以看到其實只有兩類:

1.  處理signedCookie的
2.  處理JSONCookie的，也就是內容為物件的cookie

有`加s`的函式多次調用`沒s`的函式

在signedCookie()中做的是針對字串開頭為`s:`的cookie，調用cookie-signature的unsign()方法，將密文與內容用特殊的演算法加密得出一段xxxxxx，之後再與原先的xxxxxx做比對，如果相同就表示沒改過，回傳true; 如果不同就表示被改過，回傳false，並把這個錯的signedCookie刪掉

在JSONCookie()中做的是針對字串開頭為`j:`的cookie，將cookie的內容由原先的字串轉回至物件型態，讓你能夠用物件的方法來操作cookie內容