#WKwebView

[toc]

##WKwebView与UIWebView的区别

1.主要是内存存在区别，WKWebView使用的内存空间是单独开辟的，而UIWebView使用的是应用程序程序的，一旦发生内存崩溃，WKWebView只会白屏，而UIWebView则会直接退出应用程序。
2.