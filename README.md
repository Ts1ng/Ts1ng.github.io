**NOTE**:
> - apple-app-site-association文件不能带json后缀。
> - apple-app-site-association要传到域名根目录下或者根域名下面新建的.well-known文件下。
> - Xcode工程文件Associated Domains里配置applinks:不能使用IP地址，只支持域名，域名最好用https的（如果是https域名，域名证书一定是有效被信任的证书），如果用http那么apple-app-site-association文件必须经过签名。
> - 网络顺畅的情况下，应用会在在刚安装（不是打开）的时候会去applink中的地址下载apple-app-site-association文件。
> - Universal Links will not work if you paste the link into the browser URL field.
> - Universal Links work with a user driven ```<a href="...">``` element click across domains. Example: if there is a Universal * Link on google.com pointing to bnc.lt, it will open the app.
> - Universal Links will not work with a user driven ```<a href="...">``` element click on the same domain. Example: if there is a Universal Link on google.com pointing to a different Universal Link on google.com, it will not open the app.
> - Universal Links cannot be triggered via Javascript (in window.onload or via a .click() call on an ```<a>``` element), unless it is part of a user action.
> - 总的说来，就是自从9.3.X改版之后，通用链接不支持域内跳转了，跳转前后的两个domain必须是不同的，否则只会safari打开。

**待讨论的问题**
> - 服务端是否支持https。
> - apple-app-site-association是否需要经过签名，签名之后这个文件无法明文显示安全点。
> - iOS9以下版本如何处理，是否需要Safari打开做处理。
> - 腾讯的“微下载”服务是否需要申请
> - 假设系统本地未安装兴业App的情况下，改怎么跳转？（建议跳转到微下载进行App下载，也可以通过safari来下载）
> - 如何联调内网还是外网来联调?
> - 由于universal links不支持在同一域名内打开App，页面端这方面如何处理？
> - 打开App所使用的路径问题，是统一使用一个路径加参数的方式，还是用多个路径来实现不同业务跳转？