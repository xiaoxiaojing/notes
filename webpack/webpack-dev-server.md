# 在react项目中使用 webpack-dev-server

## 1、什么是 [webpack-dev-server](http://webpack.github.io/docs/webpack-dev-server.html)
> The webpack-dev-server is a little node.js Express server, which uses the webpack-dev-middleware to serve a webpack bundle

* webpack-dev-server 是一个服务器（应该是这么理解）

## 2、安装与运行
> Using this config webpack-dev-server will serve the static files in your build folder. It’ll watch your source files for changes and when changes are made the bundle will be recompiled. This modified bundle is served from memory at the relative path specified in **publicPath (see API)**. It will not be written to your configured output directory. Where a bundle already exists at the same url path the bundle in memory will take precedence (by default)

### 安装
* 安装：`npm install webpack-dev-server --save-dev`

### 配置
* 根据文档描述，是需要配置publicPath的。所以webpack.config.js配置为
	```
	module.exports = {
	    context: __dirname + "/app",
	    entry: [
	        "./entry"
	    ],
	    output: {
	        path: __dirname + "/build",
	        publicPath: "/build",
	        filename: "bundle.js"
	    }
	};
	```
* 并且在package.json中配置
	```
	 "scripts": {
	    "iframe-start": "webpack-dev-server",
	    "inline-start": "webpack-dev-server --inline"
	  },
	```

### 运行
> The webpack-dev-server supports multiple modes to automatic refresh the page:
	Iframe mode (page is embedded in an iframe and reloaded on change)
	Inline mode (a small webpack-dev-server client entry is added to the bundle which refresh the page on change)

* 使用命令行开启服务
	* 开启iframe mode：`npm run iframe-start`
	* 开启inline mode：`npm run inline-start`

### 访问页面
* Iframe mode: `http://localhost:8080/webpack-dev-server/index.html`
* Inline mode: `http://localhost:8080/index.html`

## 3.拓展：使用node自己配置一个server
> Hot Module Replacement with node.js API
Similar to the inline mode the user must make changes to the webpack configuration.
Three changes are needed:
1.add an entry point to the webpack configuration: webpack/hot/dev-server.
2.add the new webpack.HotModuleReplacementPlugin() to the webpack configuration.
3.add hot: true to the webpack-dev-server configuration to enable HMR on the server.

### 创建一个server.js
```
var webpack = require('webpack')
var config = require('./webpack.config.js')
/**
 * 没有添加这段代码时，是不会自动刷新页面的（有的时候不希望自动刷新)
 * 添加了这段代码，就需要配置webpack.config.js
 */
//config.entry.unshift("webpack-dev-server/client?http://localhost:3000/", "webpack/hot/dev-server")
var WebpackDevServer = require('webpack-dev-server')
new WebpackDevServer(webpack(config), {
	publicPath: config.output.publicPath,
	hot: true,
	noInfo: true,
	historyApiFallback: true
}).listen(3000, '127.0.0.1', function( err, result ){
	if ( err ) {
		console.log( err )
	}
	console.log( 'Listening at localhost:3000' );
})
```
### 配置webpack.config.js
```
var webpack = require('webpack');
module.exports = {
    ......
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ]
};
```
