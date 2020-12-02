自动化项目前期准备工作：
1. 配置好本地jdk、python、pycharm环境
2. 将ui_driver目录里的chromedriver放到和python的安装目录同级目录下，当前驱动适配85.0.xx版本浏览器
   查看当前浏览器版本：浏览器输入栏输入chrome://version/ 查看内网chrome浏览器版本，如chromedriver不适配，
   在http://npm.taobao.org/mirrors/chromedriver/ 下载对应的驱动
    
3. 关闭windows系统chrome浏览器的自动更新（自动更新后驱动无法再使用） -- 如无法删除可跳过，浏览器更新后重新下载更新驱动
   关闭方法： 1. 计算机右键-点击管理，点击服务和应用程序-服务，找到Google更新服务，将两个都禁用掉。
            2. 再进入到Google安装程序的根目录，删除Update文件夹里的所有内容，并将该文件夹的属性-安全设置为全部拒绝即可
            关闭Chrome自动更新
        关闭后需要保证进入到chrome设置里的关于chrome打开显示：检查更新时出错，无法启动更新检查；如无法关闭成功等浏览器自动更新后在上面的
   地址下载最新的和浏览器匹配的驱动
   
4. 安装需要使用的包 -- pycharm下面的Terminal控制台输入以下命令，如有报错，重新再执行一遍命令
   pip install -r requirements.txt -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
5. 根据个人需要导入ui_driver里的 Monokai.icls配置

框架使用方法：
1. 首先配置yml文件里的元素定位信息，是存放整个自动化测试脚本用到的元素定位，该文件非常重要，且格式
   比较严格。定位方式有id或name时优先使用，元素定位复杂时只能使用xpath或cssSelector层级定位
2. 共8种元素定位方式：id, name, classname, tagname, xpath, cssSelector, linkText,
   partialLinkText，项目中用到的只有4种，配置时yml文件里的type名称必须和这8种一致否则找不到元素
3. yml文件配置好后运行po_generator文件生成需要用到的po文件
4. 根据业务写业务方法，在pages_manager的初始化方法里实例化第三步生成的PO类，调用
   driver_base里的公共方法和生成的po对象编写业务流程
5. 编写用例：调用业务方法，断言

注：
1. 用例之间需要独立，用例之间没有依赖关系,用例可以完全独立运行
2. 用例执行没有顺序，随机顺序都能正常执行
3. 每个用例都能重复运行，运行结果不会影响其他用例
4. .pytest_cache、allure-resules里的json文件、log日志文件、截图文件不要提交到git

