## 要点
- 利用github API获取一些github排行榜数据
- js越写越混乱，最终将所有功能模块化，封装成对象，留下一个初始化对象作为唯一的入口：
```js
    var App = {
        init: function(){
            repos.init()
            users.init()
            search.init()

        }
    }
    App.init()
```
## 遇到的问题及解决方案
- 在功能模块化后的对象里，该对象的某些函数绑定事件，事先声明好初始化的一下变量例如：`var _this = this`，从而避免`this`的含义混淆
- 分开加载每一个tab页面里的内容，从而避免滚动相互影响，如下：
```html
<body>
    <main>
        <section id="repos"></section>
        <section id="users"></section>
        <section id="search_repos"></section>
    </main>
    <footer></footer>
</body>
```
```css
main>section {
    overflow: scroll;
}
```
```
$('main>#repos').scroll(function(){
  getData()
})
$('main>#users').scroll(function(){
  getData()
})
$('main>#search_repos').scroll(function(){
  getData()
})
```
- 请求接口获取到数据后，对数据的拼接组装（考虑到页面代码不会随意恶意篡改）：
```js
        renderData: function(datas){
            var _this = this
            if(!datas)return
            datas.forEach(function(data){
                _this.num += 1
                var str =   `<div class="item">
                                <a class="html_url" href="">
                                    <div class="order"><span></span></div>
                                    <div class="detail">
                                        <h2 class="name"></h2>
                                        <div class="description"></div>
                                        <div class="watchers_count"></div>
                                    </div>
                                </a>
                            </div>`                           
                var $node = $(str)
                $node.find('.html_url').attr('href', data.html_url)
                $node.find('.order>span').text(_this.num)
                $node.find('.detail>.name').text(data.name)
                $node.find('.detail>.description').text(data.description)
                $node.find('.detail>.watchers_count').text(data.watchers_count+' star')
                $('main>section').eq(0).find('.repos').append($node)
            })
        }
```



