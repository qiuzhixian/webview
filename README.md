
# 移动端webview开发经验总结

[toc]

总结一些移动端webview的设置，及一些兼容总结

## 一、关于安卓webview组件WebSettings配置
在安卓移动端使用webview组件的时候，如果组件一些参数没有设置支持，很多功能导致无法使用，比如无法加载js，无法使用缓存等，这时就需要在webview组件做一些配置，先获取WebSettings settings = getSettings()，最后看下面做的一些例子。

### 设置编码格式
```
settings.setDefaultTextEncodingName("utf-8");
```

### 支持自动加载图片
```
settings.setLoadsImagesAutomatically(true);
```

### 设置是否允许WebView使用File协议
 默认设置为true,即允许在File域下执行任意JavaScript代码
```
settings.setAllowFileAccess(false);
```


### 设置是否允许通过 file url 加载的 Javascript 读取其他的本地文件
这个设置在 JELLY_BEAN(android 4.1) 以前的版本默认是允许，在 JELLY_BEAN 及以后的版本中默认是禁止的

```
settings.setAllowFileAccessFromFileURLs(false);
```

### 设置是否允许通过 file url 加载的 Javascript 可以访问其他的源，包括其他的文件和 http，https 等其他的源
这个设置在 JELLY_BEAN 以前的版本默认是允许，在 JELLY_BEAN 及以后的版本中默认是禁止的。
 
 ```
settings.setAllowUniversalAccessFromFileURLs(false);
```

### 设置是否保存密码
默认为true，改为false，避免造成用户的个人敏感数据泄露
```
settings.setSavePassword(false);
```
### 设置是否打开缓存。默认关闭，H5的缓存无法使用。
```
settings.setAppCacheEnabled(false);
```

### 设置缓存模式
```
// 不使用网络，只读取本地缓存数据 
settings.setCacheMode(WebSettings.LOAD_CACHE_ONLY);

// 不使用缓存，只从网络获取数据
settings.setCacheMode(WebSettings.LOAD_NO_CACHE);

// 根据cache-control决定是否从网络上取数据
settings.setCacheMode(WebSettings.LOAD_DEFAULT); 

// 只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据。
settings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK); 
```
### 设置支持DomStorage
```
settings.setDomStorageEnabled(true);
```

### 设置自适应网页大小
```
settings.setUseWideViewPort(true);
```
## 二、关于开发功能中的兼容性


在APP下需要经过webview展示和加载网页，相当于把webview当作一个浏览器，而webview就是一个简易的浏览器很多H5特性等功能还不怎么支持，就会遇到各种各样的坑。

## WebView实现文件下载功能
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

## 安卓webview支持localStorage
由于webview默认不支持localStorage，需要安卓设置
```
webSettings.setDomStorageEnabled(true);    
```

## Webview 设置支持window.open 和window.close
安卓：
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

IOS:
```
preferences.javaScriptCanOpenWindowsAutomatically = YES;  
```

## 伪类 :active 生效

- 要CSS伪类 :active 生效，只需要给 document 绑定 touchstart 或 touchend 事件，或者给body添加ontouchstart=""

```
// body添加ontouchstart=""
<body ontouchstart="" style="display: none">

// document 绑定 touchstart
document.addEventListener('touchstart',function(){},false);
```
## 禁止 IOS 弹出各种操作窗口
```
-webkit-touch-callout:none;
```
## 禁止用户选中文字
```
-webkit-user-select:none;
```
## 消除 transition 闪屏
```
// 方法一
-webkit-transform-style: preserve-3d;
/*设置内嵌的元素在 3D 空间如何呈现：保留 3D*/

// 方法一
-webkit-backface-visibility: hidden;
/*（设置进行转换的元素的背面在面对用户时是否可见：隐藏）*/
```

## -webkit-backface-visibility: hidden；兼容性
当遇到遮罩层 background-color:rgba(); 和用到 -webkit-backface-visibility: hidden;属性，会到时app，webview闪出黑色边线。

## css3动画兼容
在安卓4.4版本之前，Android WebView基于WebKit内核的实现的，需要加上-webkit前缀。
```
@-webkit-keyframes
```
## 字体使用
```
font-family: -apple-system, BlinkMacSystemFont, "PingFang SC","Helvetica Neue",STHeiti,"Microsoft Yahei",Tahoma,Simsun,sans-serif;
```

## ipone6 plus遇上输入框的固定定位,同时触发focus(),导致输入框上移，定位无效。

弹框设置固定对定位的时候不可以同时触发focus()


## Android 4.2.x 背景色溢出及不支持 border-radius 缩写

- 可以用图片代替圆角来实现
- 尝试不使用css简写模式
```
// 完整写法
border-left-radius: 999px;  /* 左上角 */
border-right-radius: 999px; /* 右上角 */
border-right-radius: 999px; /* 右下角 */
border-left-radius: 999px;  /* 左下角 */
```

## maxlength兼容性

如果使用maxlength，当用户在输入框输入回车换行，导致无法输入字

## background-color:rgba();

部分手机（r7，酷派，索尼）对background-color:rgba()的背景透明度跟标准不一样，实践比标准颜色会深些，
针对这些手机要利用到媒体查询来给这些属性作针对性修改调整。


## touchmove事件
touchmove事件在Android部分机型(如LG Nexus 5 android4.4，小米2 android 4.1.1)上只会触发一次
解决方案是在触发函数里面加上e.preventDefault(); 记得将e也传进去

## display:-webkit-flex
 三星I9100 （Android 4.0.4）不支持display:-webkit-flex这种写法的弹性布局，
 但支持display:-webkit-box这种写法的布局,
 相关的属性也要相应修改，如-webkit-box-pack: center;
 移动端采用弹性布局时，建议直接写display:-webkit-box系列的布局

## 软键盘与position:fixed兼容性
在webview开发中，获取焦点，弹出输入框，软键盘弹出，如果弹出输入框使用了固定定位，固定在底部的状态下，当用户使用的是第三方软键盘，例如QQ拼音等，输入框会被遮盖，体验极差，在我们现有技术认知水平情况下，采取的方案是被动妥协的，我们的建议是在产品设计原型上，尽量回避输入框元素出现在页面最底部的场景，例如输入框固定在顶部，或者采用了页面转场模式。


## IOS 点击会慢 300ms 问题


## [动画效果中，使用 translate 比使用定位性能高](https://paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft)


## 不支持音频自动播放
```
// 需要安卓设置属性
settings.setMediaPlaybackRequiresUserGesture( false );
```

## 不建议使用jquery和zepto实现的动画
在低端安卓手机会有各种卡顿，闪屏的bug

## line-height
line-height经常用于文字居中，不同手机显示效果不一样。什么鬼～
在chrome模拟器上又是显示得非常完美，但是！Android和IOS又各自‘偏移’了。如果把line-height加1px，iPhone文字就会稍微‘正常显示’，由于我们app的ios用户居多，并且android机型太多，不同机型也会显示不同，所以只能退而求其次了。line-height的兼容问题不太好解决，容器高度越小，显示效果的差距越明显。
解决方案：稍微大一点的高度，最好把line-height设置为高度+1px，两个平台显示都不会太‘奇怪’。

## 流畅滚动
```
-webkit-overflow-scrolling:touch;
```

## 禁止长按 a，img 标签长按出现菜单栏
```
a, img {
    -webkit-touch-callout: none;
}
```

## 防止被Iframe嵌套
```
if (window.location !== window.top.location) {
    window.top.location = window.location;
}
```

## Ios8.0系统因手机自带软件键盘弹出卡死bug
```
 document.activeElement.blur();
```

## 对于渐变的处理
有时候UI里面会有一些渐变的效果，这个时候可以用一个在线的工具，生成渐变的CSS，但是这个需要自己手动调一个和UI一模一样的效果，或者可以直接给UI调一个它理想的效果，它会生成兼容性很强的CSS：
[在线工具](http://www.cssmatic.com/gradient-generator#)


### 低端手机加载怪异（渲染不是从上到下，而是竖现出来，超怪）

可以延迟加载网页，解决出现怪异的加载效果


### 三星S7568I手机不支持background属性简写

```
// 不支持：
background: url(../img/icon_call.png) no-repeat 0 0/contain;
    
// 需改成
background-image: url(../img/icon_call.png);
background-repeat: no-repeat;
background-size: contain;
```

### IOS8系统不支持flex布局
safari 使用的是 webkit内核，在Ios8上需要单独加一下兼容才能起作用

```
display: flex;
display: -webkit-flex; 
justify-content: center;
-webkit-justify-content: center;
align-items:center;
-webkit-align-items: center;
```

### 电话号码识别
在 iOS Safari （其他浏览器和Android均不会）上会对那些看起来像是电话号码的- 数字处理为电话链接，比如：
- 7位数字，形如：1234567
- 带括号及加号的数字，形如：(+86)123456789
- 双连接线的数字，形如：00-00-00111
- 11位数字，形如：13800138000

1、关闭电话号码识别：
```
<meta name="format-detection" content="telephone=no" />
```

2、开启拨打电话功能：
```
<a href="tel:88888888">88888888</a>
```

3、开启发送短信功能：
```
<a href="sms:88888888">88888888</a>
```

### 关闭iOS键盘首字母自动大写
```
<input type="text" autocapitalize="off" />
```

### 禁止文本缩放
当移动设备横竖屏切换时，文本的大小会重新计算，进行相应的缩放，当我们不需要这种情况时，可以选择禁止：
```
html {
	-webkit-text-size-adjust: 100%;
}
```

### 设置添加到主屏幕的 Web App图标
当我们将一个网页添加到主屏幕时，除了会需要设置标题之外，肯定还需要能够自定义这个App的图标，代码如下：
```
<link rel="apple-touch-icon" href="logo.png" />
```

### 添加到主屏幕时隐藏地址栏和状态栏（即全屏）
当我们将一个网页添加到主屏幕时，会更希望它能有像 App 一样的表现，没有地址栏和状态栏全屏显示，代码如下：
```
<meta name="apple-mobile-web-app-capable" content="yes" />
```
