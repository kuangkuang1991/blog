---
title: react-native遇坑-填坑(上)
date: 2019-09-05 12:48
categories: 随笔
tags: 杂感
toc: true
---

#### 1.navigation的菜单tabbar中间按钮溢出：
参考demo：https://github.com/zhangyanlf/RNTabbar

#### 2.ant-design-rn主题方案：
     使用了global.js的全局变量 加上 组件内导入ant-design-rn的lib文件夹下的style，使用StyleSheet.flatten重新组合生成新的style
        
        import ButtonStyle from 'antd-mobile-rn/lib/button/style/index.native.js';
        ……
        function changeStyle(bgColor) {
            for (const key in ButtonStyle) {
                if (Object.prototype.hasOwnProperty.call(ButtonStyle, key)) {
                    newStyle[key] = { ...StyleSheet.flatten(ButtonStyle[key]) };
                    if (key === 'primaryRaw' || key == 'primaryHighlight') {
                        newStyle[key].backgroundColor = bgColor;
                        newStyle[key].borderColor = bgColor;
                    }

                }
            }
        }



2.0版更新：https://github.com/ant-design/ant-design-mobile/pull/1629

我的用法：

        import ButtonStyle from 'antd-mobile-rn/lib/button/style/index.native';
        let obj = { ...ButtonStyle };
        const StyleChange = (primaryColor) => {
          obj = {
            ...ButtonStyle,
            primaryHighlight: {
              borderColor:primaryColor,
              backgroundColor:primaryColor
            },
            primaryRaw: {
              borderColor:primaryColor,
              backgroundColor:primaryColor
            },
          }
          return obj;
        }
        export default StyleChange;


#### 3.在调试器（React Native Debugger）中展示network信息：
global.js中加入：

        GLOBAL.XMLHttpRequest = GLOBAL.originalXMLHttpRequest || GLOBAL.XMLHttpRequest

#### 4.界面不显示，控制台一直提示：::ffff:127.0.0.1 - - [20/Dec/2018:02:42:20 +0000] "GET /index.delta?platform=android&dev=true&minify=false HTTP/1.1" 200 - "-" "okhttp/3.11.0"

可能并不是一个报错信息，只是单纯的界面出不来，原因是调试是我把root.js里面TabNavigator内定义的两个页面互换了

PS：后来遇到同样问题，并发现了第二种原因：提前打开了chrome的debugger页面，关掉再启动就好了。

#### 5.axios拦截器里使用react-navigation跳转

按照官方文档说明(https://reactnavigation.org/docs/zh-Hans/navigating-without-navigation-prop.html
)，建立NavigationService.js文件，然后在app上注册

        import {NavigationActions} from 'react-navigation'
        // reference : https://reactnavigation.org/docs/navigating-without-navigation-prop.html
        // 该服务作为单例模式使用，用于记录顶层导航器，这样在应用的各个部分，各个屏幕组件内部，
        // 或者任何屏幕组件之外的其他服务逻辑/工具逻辑中，都可以使用该服务的 navigate() 方法导航到目标屏幕，
        // 类似于在某个屏幕组件中使用 this.props.navigation.navigate 函数进行的屏幕切换
        // 用于记录所使用的导航器
        let _navigator;
        /**
        * 记录所使用的导航器
        * @param navigatorRef
        */
        function setTopLevelNavigator(navigatorRef) {
            _navigator = navigatorRef;
        }
        /**
        * 导航到目标路由/屏幕
        * @param routeName
        * @param params
        */
        function navigate(routeName, params) {
            _navigator.dispatch(
                NavigationActions.navigate({
                    type: NavigationActions.NAVIGATE,
                    routeName,
                    params,
                })
            );
        }
        export default {
            navigate,
            setTopLevelNavigator,
        };


因为本项目用到了redux，故用以下方法：


#### 6.切换路由假死：点击页面下部的nav导航菜单，卡住

进入应用后开启app内的 remote js debug选项后打开浏览器内保存的debug页面标签

#### 7.在chrome调试页devTools的netWork中发现了 Provisional headers are shown

关闭global.js的network调试 


        // GLOBAL.XMLHttpRequest = GLOBAL.originalXMLHttpRequest || GLOBAL.XMLHttpRequest


#### 8.渐变和阴影：

渐变插件：react-native-linear-gradient

阴影：

        shadow: {
            shadowColor: 'black',
            elevation: 5,
            shadowOffset: { width: 5, height: 5 },
            shadowOpacity: 1,
            shadowRadius: 10,
            backgroundColor: '#fff', // 背景色必需
            zIndex: 999, 
            marginTop: 5,
            marginBottom: 20,
            borderRadius: 10,
        }


#### 9.code-push-server：自建react-native 的 code push热更新服务

#### 10.react-native如何组织css：

https://juejin.im/post/5a8f832c5188257a6a78dff9

#### 11.设置position:absolute不显示

scrollView组件改成普通的view组件即可

#### 12.image不规则图片只显示正方的部分

设置了宽高，width height

#### 13.webview的内容不固定高度问题：

使用onNavigationStateChange属性

        onNavigationStateChange={(title)=>{
              if(title.title != undefined) {
                  this.setState({
                    height:(parseInt(title.target)+120)
                  })
              }
        }}


#### 14.mac-ios版本下报错 'config.h' file not found
在项目目录-依赖文件夹-react-native-third-party-glog-0.3.5文件夹下执行

        执行 ../../scripts/ios-configure-glog.sh

        ./configure

        make

        make install

#### 15.蚂蚁组件react-native-rn的Popover组件，子组件不能是TouchableOpacity，否则不显示

#### 16.蚂蚁组件的Drawer组件，高度无法设置。

改为view包裹并调整样式


         drawerContainer: {
            position: 'absolute',
            zIndex: 999,
            top:0,
            right: 0,
            height: HEIGHT,
         },
         drawerBox: {        
            padding: 8,
            backgroundColor: "transparent",
            overflow: 'visible',
            position: 'absolute',
            zIndex: 999,
            right: 5,
            top: 30, 
        },


        <View style={[styles.drawerContainer, (drawerShow ? { width: WIDTH } : null)]}>
               <Drawer
                   sidebar={sidebar}
                   position="right"
                   open={false}
                   drawerRef={el => (this.drawer = el)}
                   onOpenChange={this.onOpenChange}
                   drawerBackgroundColor="#fff"
                   drawerWidth={320}
                >
                   <View style={styles.drawerBox}>
                      <TouchableOpacity onPress={() => { this.drawer && this.drawer.openDrawer() }}>
                         <Text style={styles.openBtn}>列表</Text>
                       </TouchableOpacity>
                   </View>
                </Drawer>
        </View>


#### 17.ScrollView子组件未设高度无法撑开的问题

#### 18.style使用数组和非数组的区别，数组并不是简单的合并关系

#### 19.全局开启请求调试时，无法正常发送formData请求

当开启如下调试时，

        GLOBAL.XMLHttpRequest = GLOBAL.originalXMLHttpRequest || GLOBAL.XMLHttpRequest

使用XMLHttpRequest或fetch 发送 FormData数据，返回的数据不正常，关闭后可正常使用fetch

#### 20.webview中使用injectedJavaScript不能执行

需要将插入的js压缩为一行……

#### 21.react-native自带组件FlatList不随setState更新的问题

需要添加extraData属性如 extraData={this.state}，因为FlatList是个PureComponent

#### 22.react-native自带组件FlatList的renderItem（item, i）中的i不起作用

只能识别index，无法识别i

#### 23.react native项目引入mock不起作用：

关闭下面的调试：

        // GLOBAL.XMLHttpRequest = GLOBAL.originalXMLHttpRequest || GLOBAL.XMLHttpRequest


#### 24.webview的injectJavaScript方法插入js代码不执行

原写法是

        let Options = info.Options;
        this.refs.webView.injectJavaScript(`window.onLoadPage(${Options}, null, ${this.startRadian}, ${WIDTH}, ${PIXEL})`);


直接传入对象数组无法执行（实际上是出错，但因为无法调试，效果跟没执行一样）

原因是这里的传参不能直接传对象，需要传入字符串，应该改为：


let Options = JSON.stringify(info.Options);


#### 25.react-native-table-component实现跨行和跨列
        tableData = [["10000","100","1000","0"],["10000","100","1000","0"],["30","10","20","5"],["2.00"],["备注"]]

        <Table borderStyle={styles.tableBorder}>
            <Row data={options.tableHead} style={styles.tableHead} textStyle={[styles.tableText, styles.tableHeadText]} />
            <Cols data={options.tableData} textStyle={[styles.tableText]}></Cols>
        </Table>

可以实现如下效果

#### 26.使用react-navigation的navigate()时，componentWillUnmount不会触发
原因：

        Navigator uses stack to manager route. When you push a new page,
        current page wont unmount, if you pop from new page to the current
        page, the new page will unmount. And in other scene, if you render
        some child component based on some regulation like isLoading or other,
        the componentWillUnmount method will also be called when they no
        longer need to be rendered.


推荐使用react-navigation提供的lifecycle或者replace()、pop()

推荐文章：https://www.jianshu.com/p/dc9df5826651/
