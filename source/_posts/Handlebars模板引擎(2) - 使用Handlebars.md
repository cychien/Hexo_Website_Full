---
title: Handlebars模板引擎(2) - 使用Handlebars
author: Justin
cover: /images/about-bg.jpg
tags:
categories: 
- NodeJS
date: 2017-09-14 12:47:07
---
Handlebars是一個可用於clint端或server端的模板引擎，Handlebars由javascript寫成。

## 安裝方法

在創建express時用以下命令

	express (project name) --hbs

或在不使用express-generator的情況下，使用以下命令自行加入

	npm install hbs --save

若使用第二種方法，還必須在app.js中將view engine修改為Handlebars

	app.set('view engine', 'hbs');

## Handlebars基礎

### 大括號的使用

在Handlebars模板中用 `{ { } }` 框住的內容是可以被替換的部分，像是

```handlebars
<p>Hello, {{name}}</p>
```

這樣的一個模板，如果傳入的內容是{name: 'Justin'}的話，則最終輸出的HTML將會是

	<p>Hello, Justin</p>

特別的是如果你想傳如一段HTML，像是{name: '&lt;b&gt;Justin&lt;/b&gt;'}  
使用之前的模板，輸出結果會是

	<p>Hello, &lt;b&gt;Justin&lt;/b&gt;</p>

要解決這個問題，必須在模板中使用 `{ { { } } }` ，它會關閉HTML的轉義功能:

```handlebars
<p>Hello, {{{name}}}</p>
```

### 區塊

為了解釋區塊，我們先來假設一段內容

```json
{
    name: 'Golem',
    type: ['Rock', 'Ground'],
    quickMoves: [
                {name: Rock Throw, DPS: 16}, 
                {name: Mud Slap, DPS: 12.9}
    ],
    mainMoves: [
                {name: Stone Edge, DPS: 42.9}, 
                {name: Earthquake, DPS: 35.1}, 
                {name: Rock Blast, DPS: 23.1}
    ],
    moveType: 'Rock or Ground' 
}
```

這是一段有關Pokemon Go資料的內容，現在我們來檢視一個可傳入以上內容的模板

```handlebars
<div>
    <p>Name: {{name}}</p>
    <p>
        {{#each type}}
            {{.}} 
        {{/each}}
    </p>
    <ul>
        {{#each quickMoves}}
            <li>
                Move Name: {{name}}, DPS: {{DPS}}
                {{#if ../type}}
                    ({{../../moveType}})
                {{/if}}
            </li>
        {{/each}}
    </ul>
</div>
```

原先的內容包括name, type, quickMoves, mainMoves等等屬性，但是在 `{ { #each type} }` 與 `{ { /each} }` 之間**進入了另一個"區塊"**，這個區塊的內容只包括了type的陣列，而 `{ {.} }` 則代表的是當前遍歷到的值

我們再來看看下面比較複雜的結構，一樣這個each區塊將內容改變了，變成只包含了quickMoves陣列，比較特別的是if，**在if區塊中的內容與他的父層是一樣的，但是與他的父層仍舊相隔了一層**，所以在這個例子中你可以看到，第一個 `../` 先回到父層的內容，也就是只包含了quickMoves陣列的內容，第二個 `../` 再跑到上一層，也就是回到了最原始、最完整的內容，這時就可以取用moveType的值了

千萬要記得的是: **`../` 代表的父級是就模板結構而言，不是內容本身的結構**

## 後端模板

### View & Layout

在express專案中有一個資料夾是views，當中存放著一張張的網頁(View)，不過大部分的網頁都有相似的結構，因此我們可以創造出一個Layout，Layout是一個特殊的View，是模板的模板，在hbs中，hbs會尋找名為layout.hbs的這個模板，將這個模板作為Layout

以下是一個例子: 

layout.hbs

```handlebars
<!DOCTYPE html>
<html>
    <head>
        <title>{{title}}</title>
        <link rel='stylesheet' href='/stylesheets/style.css' />
    </head>
    <body>
        {{{body}}}
    </body>
</html>
```

welcome.hbs

```html
<h1>Welcome! My Friends!</h1>
```

這時你用下列程式碼渲染頁面:

```javascript
app.get('/', function(req, res) {
    res.render('welcome', {title: 'Pokemon Go is Fun !'});
});
```

處理順序是這樣的:

1.  傳入的內容和welcome.hbs做結合
2.  之後將welcome.hbs的內容放入layout.hbs的body中，同時傳入的內容一樣會與layout.hbs做結合

最後顯示的html如下:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Pokemon Go is Fun !</title>
        <link rel='stylesheet' href='/stylesheets/style.css' />
    </head>
    <body>
        <h1>Welcome! My Friends!</h1>
    </body>
</html>
```

如果你有多個Layout，那麼你只要在傳入的內容裡指定要使用哪一個Layout就可以了，像是:

```javascript
app.get('/', function(req, res) {
    res.render('welcome', {title: 'Pokemon Go is Fun !', layout: 'layout2.hbs'});
});
```

如果你不需要套入Layout，那麼就將layout的值設為null，像是:

```javascript
app.get('/', function(req, res) {
    res.render('welcome', {title: 'Pokemon Go is Fun !', layout: null});
});
```

如果你想改變預設的Layout，那麼就更改view option的設定值，加入以下程式:

```javascript
app.set('view options', {
    layout: layout2.hbs;
})
```

### Partial

在不同的網頁上我們經常重複使用了許多相同的元件，如果將這些元件寫在每個檔案裡，不僅網頁內容變得更龐大、更難以維護，在元件的修改上也會變得十分麻煩，所以比較好的做法就是將它抽離，獨自形成一個模板，我們特別稱這種模板為"Partial"

我們先建立一個partial檔案(檔案位子在views/partials/pokemonInfo.hbs):

```handlebars
<div class="popularPokemon">
    {{#each popularPokemon}}
        <div class="pokemon">
            <img src="/images/{{imagePath}}" />
            <h3>名字: {{name}}</h3>
            <p>屬性: {{type}}</p>
            <p>外部連結: <a href="{{link}}"></a></p>
        </div>
    {{/each}}
</div>
```

再來我們要註冊partial，目的是為了在view裡面帶入這個partial，一種方式是註冊單一個partial:

```javascript
hbs.registerPartial('pokemonInfo', fs.readFileSync(__dirname + '/views/partials/pokemonInfo.hbs', 'utf-8'));
```

第二種方式是一次註冊整個資料夾內所有的內容

```javascript
hbs.registerPartials(__dirname + '/view/partials');
```

之後設定要傳入的內容，為了簡潔一點，我們用一個函式包裝:

```javascript
function getPokemonInfo() {
    return {
        popularPokemon: [
            {
                imagePath: 'Tyranitar.jpg',
                name: '班基拉斯',
                type: 'Rock、Dark',
                link: 'https://pokemon.gameinfo.io/zh-tw/pokemon/248-tyranitar'
            },
            {
                imagePath: 'Dragonite.jpg',
                name: '快龍',
                type: 'Dragon、Fly',
                link: 'https://pokemon.gameinfo.io/zh-tw/pokemon/149-dragonite'
            },
            {
                imagePath: 'Golem.jpg',
                name: '隆隆岩',
                type: 'Rock、Ground',
                link: 'https://pokemon.gameinfo.io/zh-tw/pokemon/76-golem'	
            }
        ]
    };
}
```

你可以用如下的方式傳輸數據:

```javascript
app.get('/', function(req, res) {
    res.locals = getPokemonInfo();
    res.render('welcome');
});
```

然後在welcome.hbs裡添加這個partial:

```handlebars
<h1>Welcome! My Friends!</h1>
{{> pokemonInfo}}
```

就大功告成了!  
當然這種做法還稱不上是完美，原因是你每次需要這份partial時，都要傳一次pokemonInfo內容，我們換種寫法，讓所有的view都能使用這份pokemonInfo內容，用的方式是加入中介軟體:  

```javascript
app.use(function(req, res, next) {
    res.locals = getPokemonInfo();
    next();
});
```

這樣就能為資料注入res.locals物件，而不用在每個路由處理程式裡都寫一次

### Helper

Handlebars是一個弱邏輯的模板引擎，舉個例子，你在前面看到的 `{ { #if} }` ，後面是不能接判斷式的，像 `{ { #if type!=null} }` 和 `{ { #if type&&name} }` 都是不合法的，但如果你想要處理更複雜的邏輯該怎麼辦呢?這時你就需要helper的協助了。

你可以想像helper是一個自訂義標籤，它可以在handlebars模板的任何地方，helper和partial一樣需要被事先的註冊，底下是一個簡單的helper:

```javascript
hbs.registerHelper('list', function(context, option) {
    var out = "</ul>";

    for(var i=0; i<context.length; i++) {
        out = out + "<li>" + options.fn(context[i]) + "</li>";
    }

    return out + "</ul>";
});
```

在解釋以上語法之前，我們先來看看傳入的內容(context)是什麼:

```json
{
    people: [
        {firstName: 'Justin', lastName: 'Chien'},
        {firstName: 'Jack', lastName: 'Lee'},
        {firstName: 'Mandy', lastNmae: 'Wu'}
    ]
}
```

第一個參數 `list` 是這個自定義標籤的名字，第二個參數則傳入一個function，代表你想要做到的事，function的第一個參數傳入一段context，如果沒有設則是同於當前的context，第二個參數 `option` 有一個方法 `fn` ， 假設在你的list區塊中還有其他的模板，那麼 `option.fn()` 代表的就會是編譯過的那些模板，當你傳入一段context時，它就會回傳出相應的HTML，底下是一個實際應用的例子:

```handlebars
{{#list people}}{{firstName}} {{lastName}}{{/list}}
```

嘗試理解一下，people代表現在的context已經被鎖定在people陣列中，而此時的option.fn()代表的是被編譯過的 `{ {firstName} } { {lastName} }` ，譬如將{firstName: 'Justin', lastName: 'Chien'}傳入option.fn()，它就會回傳給你 `Chien Justin` ，所以最終的結果會是這樣:

```html
<ul>
    <li>Justin Chien</li>
    <li>Jack Lee</li>
    <li>Mandy Wu</li>
</ul>
```

老實說前面看到的 `{ { #if} }` 、 `{ { #each} }` 都是helper，只是他們是內建的helper，其他內建的helper還有 `{ { #with} }` 、 `{ { #unless} }` 等等，可以在[此連結](https://segmentfault.com/a/1190000000342636)中查看

## 前端模板

在前端中一樣可以引入Handlebars，步驟是這樣的:  

1.  將Handlebars下載後放入靜態內容或直接使用CDN

2.  寫一個模板

3.  編譯模板

4.  將內容(context)傳入編譯後的模板

為了方便理解，底下是一個簡單的例子，首先引入Handlebars:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.0.10/handlebars.min.js"></script>
```

再來寫一個模板:

```handlebars
<script id="template" type="text/x-handlebars-template">
    Hi! My name is {{{name}}}.
</script>
```

然後編譯它:

```javascript
var compiledTemplate = Handlebars.compile($('#template').html());
```

最後將內容傳入編譯後的模板:

```html
<div id="result"></div>

var data = {name: 'Kevin'};
$('#result').html(compiledTemplate(data));
```

當然你一樣可以在前端模板中使用partial、helper等特性，就像Handlebars用於後端時的那樣

這篇文章主要講述一些Handlebars的基礎功能，在之後的文章中我們再來探討更多Handlebars可以做到的事