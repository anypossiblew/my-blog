---
layout:     post
title:      "用地图分析各省高考人数密度"
subtitle:   "用Turf和Nodejs并使用GeoJSON标准文件做全国各省高考报考人数的密度分析，并在地图上进行展示"
date:       2015-06-12 12:00:00
author:     "暗梅幽闻花"
header-img: "img/post-bg-05.jpg"
---

<p>据教育部网站消息，2015年全国统一高考于6月7日至8日举行。2015年全国高考报名考生共942
万人。回忆我们当年的高考，糊里糊涂的就报了志愿，为了让学弟学妹们能够尽量选择自己喜欢的学校和专业，我们有必要为他们做一些数据上的分析。接下来将要展示如何用Turf和Nodejs
做地理信息方面的数据分析，和用GeoJSON技术标准的文件在地图上展示区域图。</p>

<p>进行分析之前我们可以先了解一下两个工具，<a href="http://turfjs
.org/">Turf</a>是一个能用在浏览器或者node环境中做地理信息数据分析的javascript插件。<a href="https://nodejs.org/">Node
.js</a>是一个建立在<a href="http://code.google.com/p/v8/">Chrome's JavaScript 
runtime</a>上为方便建立快速、可扩展的网络应用而产生的平台。<a 
href="www.geojson.org/">GeoJSON</a>是一种对各种地理数据结构进行编码的格式。GeoJSON对象可以表示几何、特征或者特征集合。GeoJSON
支持下面几何类型：点、线、面、多点、多线、多面和几何集合。GeoJSON里的特征包含一个几何对象和其他属性，特征集合表示一系列特征。
如何搭建Node和npm运行环境可以自行查阅相关教程。
</p>

<h2 class="section-heading">分析计算</h2>

<p>可以clone一个repository</p>
<blockquote>git clone https://github.com/anypossiblew/analyzing-number-of-entrance
.git<br>
cd analyzing-number-of-entrance</blockquote>

<p>使用Node的包管理工具npm安装Turf</p>

<blockquote>npm install turf</blockquote>

<p>新建一个js文件index.js，加载turf和filesystem、csv模块</p>

<blockquote>var fs = require('fs');<br>
var csv = require('csv');<br>
var turf = require('turf');</blockquote>

<p>加载全国各省区域分布GeoJSON文件</p>

<blockquote>fs.readFile('china.geojson', 'utf8', function (err, data) {<br>
&nbsp;if (err) {<br>
&nbsp;&nbsp;throw err;<br>
&nbsp;}<br>
&nbsp;var china = JSON.parse(data);<br>
});</blockquote>

<p>计算每个省的面积，这里使用到了turf.area函数</p>
<blockquote>for(var i = 0; i < china.features.length; i++) {<br>
&nbsp;&nbsp;china.features[i].properties.area = (Math.round(turf.area(china.features[i].geometry) * 0.000621371))/100000;<br>
}</blockquote>

<p>加载各省高考报考人数csv文件，并用csv模块的parse功能转换成Object数组</p>
<blockquote>fs.readFile('number-of-entrance.csv', 'utf8', function (err, data) {<br>
&nbsp;if (err) {<br>
&nbsp;&nbsp;throw err;<br>
&nbsp;}<br>
&nbsp;csv.parse(data, {columns: true}, function(err, items){<br>
&nbsp;&nbsp;if (err) {<br>
&nbsp;&nbsp;&nbsp;throw err;<br>
&nbsp;&nbsp;}<br>
&nbsp;});<br>
});</blockquote>

<p>循环各省的item进行计算</p>

<blockquote>items.map(function(item) {<br>
&nbsp;for(var i = 0; i < china.features.length; i++) {<br>
&nbsp;&nbsp;if(china.features[i].properties.name == item['省'] ) {<br>
&nbsp;&nbsp;&nbsp;china.features[i].properties.register_number = item['人数']*10000;<br>
&nbsp;&nbsp;&nbsp;&nbsp;china.features[i].properties.register_density = <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(china.features[i].properties.register_number) / china.features[i].properties.area; <br>
&nbsp;&nbsp;&nbsp;}
&nbsp;&nbsp;}<br>
&nbsp;});</blockquote>
            
<p>数据计算过之后，我们要进行计算样式</p>
<blockquote>var min = 0, max = 0;<br>
for(var i = 0; i < china.features.length; i++) {<br>
&nbsp;var registerDensity = china.features[i].properties.register_density;<br>
&nbsp;if(registerDensity > max) {<br>
&nbsp;&nbsp;max = registerDensity;<br>
&nbsp;}<br>
&nbsp;if(min == 0 || min > registerDensity) {<br>
&nbsp;&nbsp;min = registerDensity;<br>
&nbsp;}<br>
}<br>
for(var i = 0; i < china.features.length; i++) {<br>
&nbsp;var opacity = Math.floor((china.features[i].properties.register_density-min)/(max-min)*10+1)/10;<br>
&nbsp;china.features[i].properties.style = {<br>
&nbsp;&nbsp;fillOpacity: opacity<br>
&nbsp;};<br>
}</blockquote>

<p>对json进行转化并写入结果文件</p>

<blockquote>fs.writeFileSync('number-of-china-entrance.geojson', JSON.stringify(china));
</blockquote>

<h2 class="section-heading">上传图形化</h2>

<p>计算得到geojson文件后，上传到网站<a href="http://www.photoshows.cn/md/#/geojson/upload">http://www
.photoshows.cn/md/#/geojson/upload</a>可以看到实际的效果图。还可以对详细的style进行自定义。</p>

<img class="img-responsive" src="{{ site.baseurl }}/img/20150612/geoJSON-upload.jpg" alt="">

