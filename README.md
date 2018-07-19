# cat
网页视频聊天室学习
webrtc 非本地使用的话需要的东西比较多, webrtc 与 java 没有关系,这里选择java做信令服务器,只是一种方式,可以用其他语言代替,网上的例子nodejs例子比较多, webrct 使用注意事项,以下一些原因会导致webrtc不能正常执行 1.首先需要浏览器支持webrtc,浏览器尽量升级到最新的版本 2.其他浏览器我不知道,谷歌浏览器的要求是,只能事127.0.0.1地址 或者 是https协议地址 运行webrtc 搜索网上其他例子会把一些其他代码混淆在一起,webrtc 只是调用浏览器内置的api,我这个代码中使用的websocket只是用来连接信令服务器,与webrtc没有关系.

2018年2月9日 17:23:23 提交内容 1.一对一对话 2.一对一文件传输

2018年3月1日 16:32:41 提交内容 1.稍微优化了一下代码的可读性 2.修复一些一对一已知的BUG 3.增加五人群组对话功能 4.五人群组文件发送  备注:由于之前没有做过这方面的经验,特意去查了一下资料 http://blog.csdn.net/gupar/article/details/53101435 这篇文章中提到了几种解决方案,以我目前的了解webrtc还是不适合做多对多的聊天,或许是我对webrtc了解的不够,这里选择的事mesh解决方案,好像webrtc只能使用这个解决方案,阅读的这篇文章还是给我提供了不少灵感,另外我在做多对多的时候碰到了一个问题 websocket发送消息时 出现 TEXT_FULL_WRITING异常,我搜索了一些资料,有的说 OnMessage 方法不要设置返回值,这只是导致这个愿意的一种,如果你给OnMessage方法设置了返回值,就把它改成void的吧,然而我并没有设置返回值,导致这个问题的原因是,相同的session中上一次发送的数据还未发送完毕,然后再次发送就会导致这个异常,然后我检查了一下 tomcat 提供的websocket服务,它默认是异步多实例模式,意思是每次有请求到后台 都是一个新的服务实例,那这个问题的根本就是多线程发送数据导致的,我暂时想到的办法是,使用 getAsyncRemote 接口发送消息,它有一个返回,这个返回对象isDone 方法为true的时候则代表它上一次发送完毕,所以我每次使用某个session对象的时候都会去检查它,具体实现在 MessageSendLockStorage 类中,这个我记录下来了,因为我百度了一下,我们国内的资料上并没有太多的解决答案,我去查了英文资料给我的灵感,我觉得这不是一个好方法,因为它会影响性能,但是它看起来是解决了这个问题,另外我自己本机测试的时候最多开3个摄像头聊天,然后cpu就爆满了,没办法单独测试五人同时视频聊天,我预感它可能还有问题.

2018年7月19日 问题记录

我做这个起初的想法只是想实时webrtc,并不会考虑兼容性,只要固定的条件上能运行就可以了,在其他浏览器是运行不起来的,起初在开发的时候  windows + tomcat8.0+谷歌浏览器 一对一能很稳定的运行,多人聊天开多了窗口会有某些窗口掉socket的情况,以及cpu占用率暴涨。

近期有人跟我反馈打开第二个窗口时与第一个窗口建立了视频聊天后会导致发起方断开websocket,我自己测试了一下确实有这个情况。

我目前的测试环境我mac + tomcat + 谷歌浏览器, 如果是使用我服务器上的地址测试应当使用 谷歌浏览器去访问,目前我也是只管谷歌浏览器，其他浏览器肯定是有兼容问题的,现在主要的问题是为什么会莫名的掉websocket连接的问题。

后期有空的时候我会自己用两台机器再次修改代码调试一下,还是打算只支持 tomcat + 谷歌浏览器,并不会考虑其他浏览器,因为我比较讨厌做前端。

再提一下,我与另一个远程机器在做调试的时候 对方是没有麦克风 摄像头等设备等,结果是发起聊天后根本不创建webrtc连接,我晚上回去会使用另一台有麦克风 摄像头的设备再次调试,确定一下这个问题。

总之有空的时候我回去看一下这些问题,我也是要工作的,不会立即去解决它。
