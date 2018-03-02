---
title: Handlebars模板引擎(1) - 關於模板引擎和Handlebars簡介
author: Justin
cover: /images/eagle.jpg
tags:
categories: 
- NodeJS
date: 2017-09-14 12:47:07
---
## 模板引擎是什麼?

有了模板引擎的協助，你可以將模板與資料分開撰寫，之後再透過模板引擎將兩者結合在一起。假設在沒有模板引擎的情況下，想要將ajax傳回來的資料填進HTML框架中，你需要這樣寫:

```javascript
$(function () {
    ​var shoesData = [{name:"Nike", price:199.00 }, {name:"Loafers", price:59.00 }, {name:"Wing Tip", price:259.00 }];
	​
    ​function updateAllShoes(shoes)  {
        var theHTMLListOfShoes = "";
	​
        shoes.forEach (function (eachShoe)  {
            theHTMLListOfShoes += '<li class="shoes">' + '<a href="/' + eachShoe.name.toLowerCase() + '">' + eachShoe.name + ' -- Price: ' + eachShoe.price + '</a></li>';
        });
        return theHTMLListOfShoes;
    }

    $(".shoesNav").append (updateAllShoes(shoesData));
});
```

這樣寫有一個最大壞處就是HTML程式碼和javascript程式碼結合在一起，當這樣子的程式碼越加龐大，你會越難看出生成出的HTML會長什麼樣子，最終導致難以維護，但若你用了模板引擎，寫法會變為這樣:

```handlebars
<script id="shoe-template" type="x-handlebars-template">​
    {{#each this}}
        <li class="shoes"><a href="/{{name}}">{{name}} -- Price: {{price}} </a></li>​
    {{/each}}
</script>
```

```javascript
$(function  () {
    var shoesData = [{name:"Nike", price:199.00 }, {name:"Loafers", price:59.00 }, {name:"Wing Tip", price:259.00 }];
    
    var theTemplate = Handlebars.compile (theTemplateScript); 
    $(".shoesNav").append (theTemplate(shoesData)); 
});
```

可以很清楚的看到模板與資料是分開的，這讓人很容易地看出生成出的HTML程式碼的樣子

## 為什麼要使用javaScript模板引擎?

- 如果你使用了javascript框架，如Backone.js, Ember.js，這些框架多半依賴於javascript模板引擎

- 如上述的例子，javaScript模板引擎可以使HTML和javascript程式碼分離

## 為什麼要使用Handlebars?

Handlebars是Mustache的擴充，他有以下的好處

- Handlebars是**弱邏輯**的模板引擎，意思是在你的模板內將不會有太多的邏輯。使用模板引擎的目的就是為了使HTML程式碼乾淨、清楚、好維護，所以寫於模板中的邏輯自然愈少愈好

- 在許多的javascript框架中你都能使用Handlebars.js，像是Meteor.js、Ember.js、Backbone.js，所以學習Handlebars是很有價值的

## Handlebars是怎麼運作的?

Handlebars是一個用javascript寫成的編譯器，它能夠接受一段包含HTML和Handlebars語法的文字，並將她轉換為一個javascript函式。

這個特別的javascript函式會接受一串資料(在此特別稱為**Context**)，將相對應的資料塞進原先的HTML，之後回傳一段包含資料的HTML。在之後的文章中會介紹Handlebars的語法，及其他重要的特性。
