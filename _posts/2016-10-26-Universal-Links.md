---
layout: post
title: 微信启动App之Universal Links开发
---

接到任务说要微信启动App看了下苹果的文档和好些人的博客感觉难度不是很大，便开搞了原来想在本机上开个Apache，配置本机的IP应该就可以差不多了。写了一个最简单的html页面，里面就放了一个超链接跳转到App的链接。没想到栽跟头了，怎么试都没跳转，找了好些文档google下说配置IP不行，于是才想起来在github.io上开搞。中间又碰到同一域名下配置的Universal Links也无法正常跳转，无奈搞了两个github.io域名才成功跳转，简单点就是当前页面的域名和即将跳转的域名不能相同。比如无法在a.github.com页面下配置的a.github.com/dispatch链接就无法跳转，得是b.github.io/dispatch才能完成跳转。其它的问题就没碰到了都是挺顺利的。

放一个网上找的Universal Links的结构图：

![_config.yml]({{ site.baseurl }}/images/universal-links.png)


**App端考虑的情况分两种就是跳转的时候App已打开和App未启动的情况**:
1. App已打开(就是App还驻留在后台)需要配置以下delegate即可：
```
#pragma mark -
#pragma mark universal links
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void(^)(NSArray * __nullable restorableObjects))restorationHandler {
    if ((userActivity && [userActivity.activityType isEqualToString:NSUserActivityTypeBrowsingWeb] && userActivity.webpageURL)) {
        ................
        return YES;
    }

    return NO;
}
```
2. App未打开时App启动后首先相应的还是```- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void(^)(NSArray * __nullable restorableObjects))restorationHandler```这个方法但是如果此时```- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions```可能还未返回所以可已在这个函数里面取得NSUserActivity对象进行处理
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  ................
  //universal links跳转到App启动时作处理
  NSDictionary *userActivityDictionary = [launchOptions objectForKey:UIApplicationLaunchOptionsUserActivityDictionaryKey];
  NSUserActivity *activity = nil;
  NSString *activityType = nil;
  if (userActivityDictionary) {
      activity = userActivityDictionary[@"UIApplicationLaunchOptionsUserActivityKey"];
      activityType = userActivityDictionary[UIApplicationLaunchOptionsUserActivityTypeKey];
  }
  if (activity && [activityType isEqualToString:NSUserActivityTypeBrowsingWeb]) {
      //处理NSUserActivity代码
      .............
  }
  return YES;
}
```

**注意事项**:

 - apple-app-site-association文件不能带json后缀。
 - apple-app-site-association要传到域名根目录下或者根域名下面新建的.well-known文件下。
 - Xcode工程文件Associated Domains里配置applinks:不能使用IP地址，只支持域名，域名最好用https的（如果是https域名，域名证书一定是有效被信任的证书），如果用http那么apple-app-site-association文件必须经过签名。> - 网络顺畅的情况下，应用会在在刚安装（不是打开）的时候会去applink中的地址下载apple-app-site-association文件。
 - Universal Links will not work if you paste the link into the browser URL field.
 - Universal Links work with a user driven ```<a href="...">``` element click across domains. Example: if there is a Universal * Link on google.com pointing to bnc.lt, it will open the app.
 - Universal Links will not work with a user driven ```<a href="...">``` element click on the same domain. Example: if there is a Universal Link on google.com pointing to a different Universal Link on google.com, it will not open the app.
 - Universal Links cannot be triggered via Javascript (in window.onload or via a .click() call on an ```<a>``` element), unless it is part of a user action.
 - 总的说来，就是自从9.3.X改版之后，通用链接不支持域内跳转了，跳转前后的两个domain必须是不同的，否则只会safari打开。

**需要讨论的问题**:

 - 服务端是否支持https。
 - apple-app-site-association是否需要经过签名，签名之后这个文件无法明文显示安全点。
 - iOS9以下版本如何处理，是否需要Safari打开做处理。
 - 腾讯的“微下载”服务是否需要申请
 - 假设系统本地未安装兴业App的情况下，改怎么跳转？（建议跳转到微下载进行App下载，也可以通过safari来下载）
 - 如何联调内网还是外网来联调?
 - 由于universal links不支持在同一域名内打开App，页面端这方面如何处理？
 - 打开App所使用的路径问题，是统一使用一个路径加参数的方式，还是用多个路径来实现不同业务跳转？

 **apple-app-site-association签名方法**
apple-app-site-association可以利用打包App的证书进行签名，在keychain里导出证书成.p12格式，具体签名步骤如下:
```
openssl pkcs12 -in Certificates.p12 -clcerts -nokeys -out output_crt.pem
openssl pkcs12 -in Certificates.p12 -nocerts -nodes -out output_key.pem
cat apple-app-site-association.json | openssl smime -sign -inkey output_key.pem -signer output_crt.pem -noattr -nodetach -outform DER> apple-app-site-association
```
