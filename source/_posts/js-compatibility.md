title: 典型的javascript兼容性问题
date: 2014-10-21 21:40:25
categories: JavaScript #文章文类
tags: [JavaScript]
---
#### 典型的javascript兼容性问题 ####

本文介绍下经常出现的javascript兼容性问题以及解决办法

``nextSibing``
    Firefox会将包括空白、换行等文本信息也当做childNodes中的一员，而IE则会忽略它，只将DOM节点当做是childNodes的一员。我么可以使用document.all 来判断，因为该属性是IE下的特有属性，而在Firefox是不支持的。可以通过判断此属性来进行相应的处理。  
    <!--more-->
``透明度``
透明度问题与上面个问题类似，IE下透明度是通过滤镜来实现的，而在firefox下透明度是通过css的opacity来实现的。我们可以进行如下设置:
```javascript
if(document.all){
a.style.filter = 'alpha(opacity = 20)';
}else{
a.style.opacity = 0.2;
}

```
则可以进行正常的设置了。  


``event对象``
  IE下event对象是window的属性作用域全局作用域，而在FIreofox中event对象是作为事件的参数存在的。可以这样获取：
e = window.event || e;
对于派生事件的对象在IE下是通过event的srcELement属性访问的，而在Firefox下是通过event对象的target属性访问的.
``冒泡``
要阻止事件冒泡，在IE下我们可以通过设置event对象的cancelBubble属性为true实现的，而在Firefox下则是通过调用event对象的stopPropagation方法实现的。
``on,attachEvent 和 addEventListemer``
要阻止事件冒泡，在IE下我们可以通过设置event对象的cancelBubble属性为true实现的，而在Firefox下则是通过调用event对象的stopPropagation方法实现的。
由于通过on xxx 监听方式没有叠加的效果，最后定义的会将前面的覆盖掉。这是一个危险的方法，我们推荐使用addEvent和addEventLister方法来代替，前者是IE支持的方法，而后者是Firefox支持的方法。
```javascript
attachEvent("onclick",function(){
});
addEventLister("click",function(){
});
```


