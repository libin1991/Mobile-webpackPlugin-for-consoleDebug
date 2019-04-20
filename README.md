## Github:[Mobile-webpackPlugin-for-consoleDebug](https://github.com/libin1991/Mobile-webpackPlugin-for-consoleDebug)

### [webpack-server-qrcode移动端扫码预览webpack插件](https://github.com/libin1991/webpack-server-qrcode?organization=libin1991&organization=libin1991)
### [eruda-vconsole](https://github.com/bywwcnll/eruda-vconsole)
### [eruda](https://github.com/liriliri/eruda)
### [eruda-webpack-plugin](https://github.com/libin1991/eruda-webpack-plugin)

---
### [帮助webpack连接文件并注入到html ](https://github.com/hxlniada/webpack-concat-plugin)
### [webpack内联代码插件](https://github.com/libin1991/webpack-inline-code-plugin)
### [insert内联 webpack 可以节省额外的 HTTP 请求](https://github.com/libin1991/html-webpack-inline-source-plugin?organization=libin1991&organization=libin1991)

---
### Description
> 移动端调试debug plugin

### Demo

![demo](https://raw.githubusercontent.com/libin1991/Mobile-webpackPlugin-for-consoleDebug/master/assets/demo.gif)
![demo4](https://raw.githubusercontent.com/libin1991/Mobile-webpackPlugin-for-consoleDebug/master/assets/4.jpg)
![demo1](https://raw.githubusercontent.com/libin1991/Mobile-webpackPlugin-for-consoleDebug/master/assets/2.jpg)
![demo2](https://raw.githubusercontent.com/libin1991/Mobile-webpackPlugin-for-consoleDebug/master/assets/3.jpg)
![demo3](https://raw.githubusercontent.com/libin1991/Mobile-webpackPlugin-for-consoleDebug/master/assets/1.jpg)


### use
```
cnpm i mobile-webpackplugin-for-consoledebug  —D
```
### webpack.config.js
```
const consoleDebugWebpackPlugin = require('mobile-webpackplugin-for-consoledebug');

...

module.exports = {
    plugins: [
        new consoleDebugWebpackPlugin()
    ]
};
```

### Vue.config.js
```
const IS_PROD = ['production', 'prod'].includes(process.env.NODE_ENV); //生产环境
const IS_DEV = ['development', 'dev'].includes(process.env.NODE_ENV); //开发环境
let path = require('path')
let package = require('./package.json')
let glob = require('glob')
var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const fs = require('fs-extra')
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');    //清除注释
const consoleDebugWebpackPlugin = require('mobile-webpackplugin-for-consoledebug');   //移动端调试
const WebpackServerQRcode = require('@ice-point/webpack-server-qrcode');    //移动端扫码
//配置pages多页面获取当前文件夹下的html和js
function getEntry(globPath) {
	let entries = {};
	glob.sync(globPath).forEach(function(entry) {
		var tmp = entry.split('/').splice(-3);
		console.log(tmp)
		entries[tmp[1]] = {
			entry: 'src/' + tmp[0] + '/' + tmp[1] + '/index.js',
			template: 'src/' + tmp[0] + '/' + tmp[1] + '/' + tmp[2],
			filename: tmp[1] + '.html'
		};
	});
	return entries;
}

let pages = getEntry('./src/pages/**?/*.html');
console.log(pages)

module.exports = {
	lintOnSave: false, //禁用eslint
	baseUrl: process.env.NODE_ENV === "production" ? `//www.mycdn.cn/${package.name}/` : '/',
	productionSourceMap: false,
	pages,
	devServer: {
		disableHostCheck: true,
		index: '/', //默认启动serve 打开/页面
		open: process.platform === 'darwin',
		host: '',
		port: 9527,
		https: false,
		hotOnly: false,
		before: app => {
			app.get('/', (req, res, next) => {
				for(let i in pages) {
					res.write(`<a target="_self" href="/${i}.html">/${i}.html</a></br>`);
				}
				res.end()
			});
			app.get('/mock/:goods/:list', (req, res, next) => { //mock数据
				var json = fs.readJsonSync('./src/mock/index.json')
				var {params,query} = req;
				res.status(299).json({json,params,query}).end();
			})
		}
	},
	configureWebpack: config => {
		console.log(config)
		config.devServer={    //显示二维码需要此配置
			host: '0.0.0.0'
		}
		config.performance = {   //禁止打包警告
			hints: false
		}
		var plugins=[];
		if(IS_DEV){
			plugins=[new consoleDebugWebpackPlugin(),new WebpackServerQRcode()];
		}
		
		plugins.push( //清除控制台打印
			new UglifyJsPlugin({
				uglifyOptions: {
					compress: {
						// 在UglifyJs删除没有用到的代码时不输出警告
						warnings: false,
						// 删除所有的 `console` 语句，可以兼容ie浏览器
						drop_console: true,
						drop_debugger: true,
						// 内嵌定义了但是只用到一次的变量
						collapse_vars: true,
						// 提取出出现多次但是没有定义成变量去引用的静态值
						reduce_vars: true,
						pure_funcs: ['console.log'] //移除console
					},
					output: {
						// 最紧凑的输出
						beautify: false,
						// 删除所有的注释
						comments: false,
					}
				},
				sourceMap: false,
				parallel: true
			})
		);
		config.plugins = [
			...config.plugins,
			...plugins
		];
	}
}
```



### eruda-webpack-plugin
```
let path = require('path')
let glob = require('glob')
var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const fs = require('fs-extra')
const consoleDebugWebpackPlugin = require('mobile-webpackplugin-for-consoledebug');
const ErudaWebpackPlugin = require('eruda-webpack-plugin')


//配置pages多页面获取当前文件夹下的html和js
function getEntry(globPath) {
	let entries = {};
	glob.sync(globPath).forEach(function(entry) {
		var tmp = entry.split('/').splice(-3);
		console.log(tmp)
		entries[tmp[1]] = {
			entry: 'src/' + tmp[0] + '/' + tmp[1] + '/index.js',
			template: 'src/' + tmp[0] + '/' + tmp[1] + '/' + tmp[2],
			filename: tmp[1] + '.html'
		};
	});
	return entries;
}

let pages = getEntry('./src/pages/**?/*.html');
console.log(pages)

module.exports = {
	lintOnSave: false, //禁用eslint
	baseUrl: process.env.NODE_ENV === "production" ? '//conchfairy.sinajs.cn/music/' : '/',
	productionSourceMap: false,
	pages,
	devServer: {
		index: '/', //默认启动serve 打开/页面
		open: process.platform === 'darwin',
		host: '',
		port: 9527,
		https: false,
		hotOnly: false,
		before: app => {
			app.get('/', (req, res, next) => {
				for(let i in pages) {
					res.write(`<a target="_self" href="/${i}.html">/${i}.html</a></br>`);
				}
				res.end()
			});
			app.get('/mock/:goods/:list', (req, res, next) => {  //mock数据
				var json=fs.readJsonSync('./src/mock/index.json')
				var {params,query}=req;
				res.status(299).json({json,params,query}).end();
			})
		}
	},
	chainWebpack: config => {

	},
	configureWebpack: {
		plugins: [
		    new consoleDebugWebpackPlugin(),
		    new ErudaWebpackPlugin({   // 'src/pages/about/index.js'
		     
		    }),
			process.env.NODE_ENV === "production" ? function() {} : new BundleAnalyzerPlugin()
		]
	}
}
```
