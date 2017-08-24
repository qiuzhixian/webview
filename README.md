# webview下开发的坑


**在APP下需要经过webview展示和加载网页，相当于把webview当作一个浏览器，而webview就是一个简易的浏览器
很多H5特性等功能还不怎么支持，就会遇到各种各样的坑。
问题有：**

[toc]

## 1、伪类 :active 生效

- 要CSS伪类 :active 生效，只需要给 document 绑定 touchstart 或 touchend 事件

```
<style>
a {
  color: #000;
}
a:active {
  color: #fff;
}
</style>
<a herf=foo >bar</a>

<script>
  document.addEventListener('touchstart',function(){},false);
</script>
```
## 2、禁止 iOS 弹出各种操作窗口
```
-webkit-touch-callout:none
```
## 3、禁止用户选中文字
```
-webkit-user-select:none
```
## 4、消除 transition 闪屏
```
方法一
webkit-transform-style: preserve-3d;
/*设置内嵌的元素在 3D 空间如何呈现：保留 3D*/
方法一
-webkit-backface-visibility: hidden;
/*（设置进行转换的元素的背面在面对用户时是否可见：隐藏）*/
```
## 5、css3动画兼容
```
加-webkit
写法@-webkit-keyframes
```
## 6、字体使用
```
font-family: "Helvetica Neue", Helvetica, STHeiTi, sans-serif;
```
## 7、安卓APP内存溢出
```
H5页面很多图片会导致APP内存溢出
H5缓解行解决方案：
1，压缩图片 http://zhitu.isux.us/   https://tinypng.com/
2，代码优化

```
## 8、ipone6 plus遇上输入框的固定定位,同事触发focus(),导致输入框上移，定位无效。
```
弹框定位不可以同时触发focus()
```

## 9、三星S5不支持border-radius缩写（圆角）
```
1，用图片代替圆角
2，完整写法
    border-left-radius: 999px; /* 左上角 */
    border-right-radius: 999px; /* 右上角 */
    border-right-radius: 999px; /* 右下角 */
    border-left-radius: 999px; /* 左下角 */
```
## 10、webview下maxlength兼容性
```
如果使用maxlength,当用户在输入框输入回车换行，导致无法输入字
```
## 11、-webkit-backface-visibility: hidden；兼容性
```
这个属性可以消除 transition 闪屏用到
部分手机（r7，酷派，索尼），当用到遮罩层background-color:rgba();，和用到-webkit-backface-visibility: hidden;属性，会到时app，webview闪出黑色边线。
```

## 12、background-color:rgba();
```
部分手机（r7，酷派，索尼）对background-color:rgba()的背景透明度跟标准不一样，实践比标准颜色会深些，
针对这些手机要利用到媒体查询来给这些属性作针对性修改调整。
```
## 13、touchmove事件
```
touchmove事件在Android部分机型(如LG Nexus 5 android4.4，小米2 android 4.1.1)上只会触发一次
解决方案是在触发函数里面加上e.preventDefault(); 记得将e也传进去。
```

## 14、display:-webkit-flex
```
 三星I9100 （Android 4.0.4）不支持display:-webkit-flex这种写法的弹性布局，
 但支持display:-webkit-box这种写法的布局,
 相关的属性也要相应修改，如-webkit-box-pack: center;
 移动端采用弹性布局时，建议直接写display:-webkit-box系列的布局
```
## 15、软键盘与position:fixed兼容性
http://www.th7.cn/web/html-css/201501/74695.shtml
```
在webview开发中，获取焦点，弹出输入框，软键盘弹出，如果弹出输入框使用了固定定位，固定在底部的状态下，当用户使用的是第三方软键盘，例如QQ拼音等，输入框会被遮盖，体验极差，在我们现有技术认知水平情况下，采取的方案是被动妥协的，我们的建议是在产品设计原型上，尽量回避输入框元素出现在页面最底部的场景，例如输入框固定在顶部，或者采用了页面转场模式。
```
## 16、iOS 点击会慢 300ms 问题
 [https://developers.google.com/mobile/articles/fast_buttons?hl=de-DE](https://developers.google.com/mobile/articles/fast_buttons?hl=de-DE "article5")
 [http://stackoverflow.com/questions/12238587/eliminate-300ms-delay-on-click-events-in-mobile-safari](http://stackoverflow.com/questions/12238587/eliminate-300ms-delay-on-click-events-in-mobile-safari "article5")

## 17、动画效果中，使用 translate 比使用定位性能高
<http://paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/>


## 18、安卓webview不支持音频自动播放
需要安卓设置属性
settings.setMediaPlaybackRequiresUserGesture( false );


## 19、不建议使用jq的动画

## 20、line－height
line-height经常用于文字居中，不同手机显示效果不一样。什么鬼～
在chrome模拟器上又是显示得非常完美，但是！Android和IOS又各自‘偏移’了。如果把line-height加1px，iPhone文字就会稍微‘正常显示’，由于我们app的ios用户居多，并且android机型太多，不同机型也会显示不同，所以只能退而求其次了。line-height的兼容问题不太好解决，容器高度越小，显示效果的差距越明显。
解决方案：稍微大一点的高度，最好把line-height设置为高度+1px，两个平台显示都不会太‘奇怪’。

## 20、流畅滚动
```
body{
    -webkit-overflow-scrolling:touch;
}
```

## 22、禁止长按 a，img 标签长按出现菜单栏
```
a, img {
    -webkit-touch-callout: none; /*禁止长按链接与图片弹出菜单*/
}
```

## 23、防止被Iframe嵌套
```
if(top != self){
    location.href = ”about:blank”;
}
```

## 24、H5禁止手机自带键盘弹出(解决ios8.0因手机自带软件键盘弹出卡死bug)
```
  $("#input").focus(function () {
     document.activeElement.blur();
    });
```

## 25、安卓webview支持localStorage
由于webview默认不支持localStorage，需要安卓设置
```
webSettings.setDomStorageEnabled(true);    
```
## 26、WebView实现文件下载功能
```
WebView控制调用相应的WEB页面进行展示。当碰到页面有下载链接的时候，点击上去是一点反应都没有的。原来是因为WebView默认没有开启文件下载的功能，如果要实现文件下载的功能，需要设置WebView的DownloadListener，通过实现自己的DownloadListener来实现文件的下载。具体操作如下： 

1、设置WebView的DownloadListener： 
    webView.setDownloadListener(new MyWebViewDownLoadListener()); 

2、实现MyWebViewDownLoadListener这个类，具体可以如下这样：  
Java代码
private class MyWebViewDownLoadListener implements DownloadListener{  
  
        @Override  
        public void onDownloadStart(String url, String userAgent, String contentDisposition, String mimetype,  
                                    long contentLength) {             
            Log.i("tag", "url="+url);             
            Log.i("tag", "userAgent="+userAgent);  
            Log.i("tag", "contentDisposition="+contentDisposition);           
            Log.i("tag", "mimetype="+mimetype);  
            Log.i("tag", "contentLength="+contentLength);  
            Uri uri = Uri.parse(url);  
            Intent intent = new Intent(Intent.ACTION_VIEW, uri);  
            startActivity(intent);             
        }  
    }  
```

## 27、对于渐变的处理
有时候UI里面会有一些渐变的效果，无法复制CSS出来，这个时候可以用一个在线的工具，生成渐变的CSS：http://www.cssmatic.com/gradient-generator#，但是这个需要自己手动调一个和UI一模一样的效果，或者可以直接给UI调一个它理想的效果，它会生成兼容性很强的CSS：
```
background: #fff;
background: -moz-linear-gradient(left, #fff 0%, #d2d2d2 43%, #d1d1d1 58%, #fefefe 100%);
background: -webkit-gradient(left top, right top, color-stop(0%, #fff), color-stop(43%, #d2d2d2), color-stop(58%, #d1d1d1), color-stop(100%, #fefefe));
background: -webkit-linear-gradient(left, #fff 0%, #d2d2d2 43%, #d1d1d1 58%, #fefefe 100%);
background: -o-linear-gradient(left, #fff 0%, #d2d2d2 43%, #d1d1d1 58%, #fefefe 100%);
background: -ms-linear-gradient(left, #fff 0%, #d2d2d2 43%, #d1d1d1 58%, #fefefe 100%);
background: linear-gradient(to right, #fff 0%, #d2d2d2 43%, #d1d1d1 58%, #fefefe 100%);
filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#fff', endColorstr='#fefefe', GradientType=1 );
```


## 28.Webview 设置支持window.open 和window.close
```
WebSettings ws = mWebView.getSettings();  
        ws.setJavaScriptEnabled(true);   
        ws.setJavaScriptCanOpenWindowsAutomatically(true);  
        ws.setSupportMultipleWindows(true);  
class ChromeClient extends WebChromeClient {  
          
        @Override  
        public void onCloseWindow(WebView window) {  
            //TODO something  
            super.onCloseWindow(window);  
        }  
  
        @Override  
        public boolean onCreateWindow(WebView view, boolean isDialog,  
                boolean isUserGesture, Message resultMsg) {  
  
            //TODO something  
  
            return true;  
        }  

```