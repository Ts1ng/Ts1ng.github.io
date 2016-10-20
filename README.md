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