# 如何写一个的vue公共组件
## Build Setup
``` 
#安装依赖
npm install
#本地开发 localhost:8098
npm run dev
```
## 前言
一个前端小开发仔，封装一个适销对路的组件，是我们必备的基本功。
本文以实现一个vue的滚动监听组件，总结一下如何封装一个造福后人(能用)的公共组件。

## 需求场景
H5应用中，为了减轻后台查询数据的压力，列表常以滚动加载的形式实现。
即首次进入页面时，只请求若干条数据。当页面滚动到底部时，再次请求接口返回数据。

## 需求分析
由于这种业务场景十分常见，因此可封装一个通用的滚动监听组件，监听页面滚动的位置。当触发特定条件时，向外派发一个事件。

### 功能实现
#### 页面滚动监听
1. 当父元素的固定高度 **<** 内部子元素的总高度,且父元素 **overflow: scroll** 时，可以监听到父元素的 **scroll** 事件。因此，滚动列表需要插在scroll里。这里我们需要用到一个 **slot** 来实现。
2. 由于需要绑定DOM事件，需要获得对DOM的控制。这里我们需要用到一个 **refs** 来实现。
3. 当触发监听的条件时，我们需要对调用scroll组件的组件派发一个事件，以通知进行下一步的逻辑。这里我们需要用到一个 **emit** 来实现。

*至此，一个滚动组件的主要功能就出来了。但是，作为一个有追求的组件，我们是不是还可以改进一下呢。*

#### 优化
##### 防抖
滚动事件触发频率很高，因此可以做一个防抖操作，只在最后一次滚动事件触发时做判断滚动位置的操作。

##### 绑定移除
当后台无相关数据时，就不需要再监听滚动事件，写一个解除绑定的方法，以供按需调用。

##### 解决惯性滚动问题
手机实测时，滚动事件只在手指滑动接触屏幕时触发滚动事件。因此，当判定未到达底部时，写一个延时判断逻辑，判断是否在惯性滚动其间，元素滚动到底部。(各位大佬有其他解决办法，可以评论提一下)

**注：记得添加** *-webkit-overflow-scrolling: touch;* **否则IOS端不实现惯性滚动**

##### 增加通用性
当前设计下，触发派发事件的滚动位置、防抖的间隔、refs的值都是固定的。

但对于不同列表，可能需要提早做一些预加载数据、操作一些滚动组件的DOM等。

因此这些常用的参数，应该通过 **props** 对外暴露出来，让父组件传入。

如果定义了一个是Object类型的props，可能会出现某些字段不传的情况。

这时，组件里利用 **三元表达式** ，Object.key ？ Object.key : defaultValue ，可使得组件有良好的默认表现。

效果如下：

![滚动组件效果.gif](https://upload-images.jianshu.io/upload_images/17015329-b8e53d4fd8e429b3.gif?imageMogr2/auto-orient/strip)

demo地址：https://github.com/yejiayuan163/simpleScroll

##### 总结

写完一个滚动组件的开发流程，现在可以总结一下开发一个vue公共组件的原则
1. **只关注公共组件自身的功能。** 公共组件只关注自己的状态，不关心页面数据逻辑。
2. **设计好与父组件的通信。** 父组件通过props控制公共组件的表现。通过emit的事件，监听到公共组件状态。通过refs调用到公共组件的方法。当然用vuex+computed属性监听也是可以的。
3. **有良好的默认表现** 使用者既可定制组件的表现，也可以简单地使用默认配置。
4. **记得写使用文档。**
