#### 构建目标
* 当图片加载失败时，不做显示
* 图片进行预加载，提升用户体验
* 每次添加图片的时候保证往最短的列插入
* 保证图片比例，高度由图片高度等比例撑开
* 参数变化时触发页面更新
```
// 安装开发依赖
npm i vue-falls-optimize --save
```
* 安装成功后，在项目中引入
```
// main.js 中全局引入 注意 Vue.use要在new Vue实例之前
import fallsOptimize from 'vue-falls-optimize'
Vue.use(fallsOptimize)
```
* 组件内使用
```
 <vue-falls-optimize :photoList="photoList" :row="row" @onClick="click" @clickLove="clickLove" width="600"></vue-falls-optimize>
```
* 参数说明

| 参数 |   默认值  | 类型 | 示例 | 说明 |
|-----|-----|---|------|----|
| photoList  | [] | array| 看下方 |看下方 |
| row  | 2 | number | 3 | 要显示的排数，默认显示2排|
| width  |  100%  | string | '600 ' |  整个容器的宽度，默认100% |
示例：

```
photoList：[{
title: "asdasdas",//标题
image:["https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=2527007796,4273119730&fm=26&gp=0.jpg"], //图片
portrait:"https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=387639067,1599589691&fm=26&gp=0.jpg",//头像
user_name:"111",//用户名
give:99,//点赞数
give_state:1,//是否点赞(1:是,2:否)
type:1,//文章类型(1:图片文章,2:视频文章)
video:""//视频链接
}]
```

==现在只支持七九云视频自动截取第一帧作为封面，其他的如果有需求请联系我下方邮箱或者github留言==



* 事件回调
  onClick点击每一个图片时候触发，返回当前点击photoList的item

  onClickLove点击每一个爱心时候触发，返回当前点击photoList的item

* 维护
email:1592791721@qq.com
github:https://github.com/azhaaa/vue-falls-optimize
