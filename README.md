# vipkwd-poster

Uni App 小程序海报通用生成组件

---
![viewer.gif](https://github.com/wxy545812093/vipkwd-poster/raw/master/static/viewer.gif)

## 插件安装方法

方法1、使用`HBuilderX`导入插件;

方法2、下载Github仓库[ 示例源码](https://github.com/wxy545812093/vipkwd-poster)，自主导入进本地项目中;

---

---

###### --- Github仓库源码方式安装步骤：

1、下载仓库目录( `/components` )覆盖放置本地项目根下的组件目录( `/components` )`如本地项目已启用分包，请按需求自行处理）` ;

```
/components
/components/vipkwd-poster
/components/vipkwd-poster/index.vue
/components/vipkwd-poster/wecanvas.vue
/components/...
/components/...
```

2、导入静态资源文件(`/static/vipkwd-poster`)。注意: 本项目中的组件代码部分没有引用任何图片资源， 此目录下的静态图片是demo数据，生产项目中可直接删除;

```
/static
/static/vipkwd-poster
/static/vipkwd-poster/demo-banner.jpg
/static/vipkwd-poster/demo-wxacode.jpeg
/static/...
/static/...
```

3、本地项目中导入此组件(参见：/pages/poster/index.vue)
```
<template>
	<poster :config="config" />
</template>

<script>
	import poster from '@/components/vipkwd-poster/index'
	import Data from './demoData'; //海报数据结构
	export default {
		components: {
			poster
		},
		data() {
			return {
				config: Data.demo,
			}
		},
		
		...
		...
	}
</script>
```


## 组件参数说明

```
<poster ref="poster" :hideLoading="false" :preload="false" :config="config" @success="onPosterSuccess" @fail="onPosterFail" />
```

| 参数 | 类型 | 必填项 | 默认值 | 描述 |
| :-----| ----: | :----: | :----: | :----: |
| config | Object | 是 | - | 待绘制海报的 结构化原始数据 |
| ref | String | 否 | - | 为组件注册引用，以实现异步绘制海报,暴露方法: onCreate |
| hideLoading | Boolean | 否 | false | 为true时,不显示“绘制中”提示loading效果 |
| preload | Boolean | 否 | false | 为true时，将预下载 config中的远程图片 |
| success | ClickEvent | 否 | - | 海报绘制成功回调 |
| fail | ClickEvent | 否 | - | 海报绘制错误回调 |


## 结构化原始数据解译(所有绘制数据（如有）必须写在 blocks/lines/texts/images 4节点下)
```
export default {
	demo:{
		width: 750, //画布预设宽度（绘制的作品将是此宽度的一半）
		height: 1000, //画布预设高度（绘制的作品将是此高度的一半）
		backgroundColor: '#fff',//画布背景
		//debug: 0,
		pixelRatio: 1, //像素比例
		
		//块数据
		blocks: [
            {
                width: 690,
                height: 808,
                x: 30,
                y: 183,
                borderWidth: 2,
                borderColor: '#f0c2a0',
                borderRadius: 20,
            },
            {
                width: 634,
                height: 74,
                x: 59,
                y: 770,
                backgroundColor: '#fff',
                opacity: 0.5,
                zIndex: 100,
            },
        ],
        //文本数据
		texts:[
			{
				//文本绘制起笔位置
				x: 20,
				y: 400,
				//基线对齐方式
                baseLine: 'middle',
                //文本节点
				text:[
                    {
                        text: '￥159.00',
                        fontSize: 36,
                        color: '#f00', //16进制色值
                        opacity: 1,
                        marginLeft: 10,
                        // textDecoration: 'line-through',//带删除线的文字
                    },
				]
			},
			{
				x: 20,
				y: 450,
                baseLine: 'middle',
				text:[
                    {
                        text: '长标题长标题长标题长标题长标题长标题长标题长标题长标题',
                        fontSize: 30,
                        color: '#ccc',
                        opacity: 1,
                        marginLeft: 0,
                        marginRight: 10,
                        width: 450, 文本绘制容器宽度，建议配合"lineNum"使用
                        lineHeight: 40,
                        lineNum: 2, //文本最多占据2行，超宽加"..."
                    },
				]
			},
		],
		
		//线条数据
		lines:[
			{
				//线条开始位置
				startX: 0,
				startY: 350,
				//线条结束位置
				endX:750,
				endY:350,
				//线条宽度
				width: 1,
				//线条颜色
				color: '#d1d1d1'
			}
		],
		
		//图片数据
		images:[
			{
				//url: 'https://static.demo.com/xxx.jpg',
				url: '/static/vipkwd-poster/demo-banner.jpg',
				//绘制到画布的图片宽度
				width: 650,
				//绘制到画布的图片高度
				height:300,
				//在画布上定位开始绘制位置
				x: 30,
				y: 10
			},
			{
				url: '/static/vipkwd-poster/demo-wxacode.jpeg',
				width: 100,
				height:100,
				x: 550,
				y: 410
			}
		],

	}
}


```