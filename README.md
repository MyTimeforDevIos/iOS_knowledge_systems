# 项目概况 

## 开发环境部署
 - Swift 5.0 

## 功能分析
### 核心功能 - 练习考试  
    https://github.com/Zuikyo/ZIKViper/blob/master/README-CN.md
    https://objccn.io/issue-13-5/
    
    > 单个模块分为三个功能，欢迎界面，题目详情，答题卡
    1. 考试功能模块分为三部分 欢迎界面 + 题目详情 + 答题卡
            所以想在此做一个统一入口，集中管理三个文件
    2. 使用 VIPER 架构
    
    > Build
        初始化一个完整的 VIPER 模块，每个模块初始化以及添加对应的依赖
        
    - View  
        确定考试界面布局。不做任何业务或数据处理，确定界面以及响应点击事件通过代理传递给 Presenter 处理
    - Presenter 
        连接 view 和 interactor 。负责 View 交互响应事件和业务数据处理上事件的响应（event 和 datasource）
    - Interactor
        封装业务相关代码。获取数据，处理解析数据。持有业务数据必要的状态
    - Wireframe
        封装好了源界面和目的界面，以及跳转需要的数据提供给模块内部使用，用于从一个模块到另一个模块
    - Entity 
        数据模型
    
    > 前五者基本可以实现 VIPER 结构，如果需要更清晰地解耦，添加以下两个
    - Service
        提供封装好的通用服务。数据库访问，文件下载等。使用时，注入到 Builder 中，再从 Builder 中注入到 Interactor。
    - Route 
        提供具体的跳转方法实现。在 Wireframe 中调用，封装由 UIKit 提供的条状方法。
    - Adapter
        Route 可以理解为面向接口编程中的抽象类。而 Adapter 就是被调用的每个接口

    > 考试/练习 入口 
    - InfomationWebViewController 练习满分获奖入口

### 网页内跳转 原生界面/新网页/考试

    InfomationWebViewController 

    func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) 方法

### 电子书框架 https://github.com/GGGHub/Reader
    路径和编译设置 https://github.com/GGGHub/Reader/issues/56
    当前技能中油和测试服 target 已经添加成功，其他的需要重新添加
    - 框架修复添加了付费按钮 pay

### 沙盒保存路径 
    urlStrMD5 是路径的 MD5 编码
    学习资料（视频，pdf ） "/Documents/KnowledgeSharing" + "/" + urlStrMD5 + "." + "mp4"

### 直播 
    SDK 使用的阿里云 v4.5 版本播放器
    因为选用的是 ARM 文件夹的带 bitcode 的 SDK
        在打包过程中取消勾选 Rebuild from Bitcode 选项。否则会出现 App 点击闪退的错误
        不能在模拟器上显示直播数据，会出现 debug 失败
    需要打包上传是时，需要在 embedded binaries 中换成 ARM 中的 SDK
    在模拟器中运行的时候，需要换成 ARM_SIMULATOR 中的 SDK

    有时会出现运行时动态库链接错误，将 TARGET - General - Frameworks，Libraries，and Embedded Content 中对应的直播框架，“Do Not Embed” 设置成 “Embed&Sign”
    
### 弹幕 
    https://github.com/kingly09/KYBarrageKit


### 长链接   
    https://github.com/tuyaohui/IM_iOS 学习资料
    https://github.com/facebook/SocketRocket 长链接框架

### 网页拉起 App
    利用的是  Universal Links （iOS 9.0 之后支持）
    [参考文档] (https://www.jianshu.com/p/f1a1e1833eec)
    [苹果官方文档](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW1)
    
    1. 打开 Associated Domains 功能。(对应的会在文件目录中出现 APPNAME.entitlements 文件)
    2. 创建一个 apple-app-site-association 文件(不需要任何后缀名)，内容如下
    
        {
            "applinks": {
                "apps": [],
                "details": [
                    {
                        "appID": "TEAMIDSHSAUX.com.test.bundle",
                        "paths": [ "*" ]
                    }
                ]
            }
        }
        
        appID 对应的数据是在开发者中心找到 teamID 拼接上对饮的 appID
        paths 对应的是打开 App 时对应的具体页面，"*" 表示只是打开 App
        
    3. 此文件存放在服务中的位置，要和在 Associated Domains 对应填入的地址一致
        testServer 中地址是  guangdong.c.dxtek.net。
        
        文件放在 https://guangdong.c.dxtek.net/apple-app-site-association
        Associated Domains 填入的地址是 applinks:guangdong.c.dxtek.net
        
    4. 重新编译 App 
        [测试 apple-app-site-association 文件是否可用地址](https://search.developer.apple.com/appsearch-validation-tool/)

### 新增 Target

    1. target 右键 duplicate 出新的 target  - 修改新 target 的名字 - 在 manage scheme 中修改对应名称

    2. LoginAndRegisterViewController 文件 viewDidLoad() 方法中新增设置固定服务器地址 setExplicitServerAddress（）

    3. AppBundleStruct 文件中新增对应服务器相关参数

    4. 修改 appTitle 对应数据
     新增 LaunchStoryboard，
     修改 plist 对应名称（在 build setting 修改 info.plist 对应路径）

### 弹窗
    1. 直播中验证密码弹窗  LiveBroadcastAuthentication 单个输入框，有 Bool 数据回调
    2. 练习中获奖弹窗 WinningTipsSheetView 两个输入框，只有 view.dismiss 回调

### 
    https://github.com/LINGLemon/XFAVFoundation
