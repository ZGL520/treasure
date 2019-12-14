安装配置node环境
-

1.nvm去github上找到Windows版本下载，这里下载nvm-noinstall，然后解压

2.点击install.cmd会看到弹出一个settings.txt,把当前路径（就是nvm-noinstall解压的路径，建议C：\Develop\nvm-noinstall)复制进去敲回车 

3.做如下配置并保存（注意360之类的软件以及防火墙都关闭）

    root: C:\Develop\nvm-noinstall 
	path: C:\Develop\nodejs 
	arch: 64 
	proxy: none

4.环境配置

配置用户变量--路径是控制面板->系统和安全->系统->高级系统设置->环境变量

注意不要改变系统变量

在用户变量里新建变量名

	变量名  NVM_HOME		变量值  C:\Develop\nvm-noinstall

	变量名  NVM_SYMLINK    变量值  C:\Develop\nodejs

psth里面添加如下

	%NVM_HOME%;%NVM_SYMLINK%

5.测试

在任意目录下打开命令工具，输入如下
	
	nvm -v

如果执行结果如下则配置完成

	Running version 1.1.2.

	Usage:

  		nvm arch                     : Show if node is running in 32 or 64 bit mode.
  		nvm install <version> [arch] : The version can be a node.js version or "latest" for the latest stable version.
                                 		Optionally specify whether to install the 32 or 64 bit version (defaults to system arch).
                                 		Set [arch] to "all" to install 32 AND 64 bit versions.
                                 		Add --insecure to the end of this command to bypass SSL validation of the remote download server.
  		nvm list [available]         : List the node.js installations. Type "available" at the end to see what can be installed. Aliased as ls.
  		nvm on                       : Enable node.js version management.
  		nvm off                      : Disable node.js version management.
  		nvm proxy [url]              : Set a proxy to use for downloads. Leave [url] blank to see the current proxy.
                                 		Set [url] to "none" to remove the proxy.
  		nvm node_mirror [url]        : Set the node mirror. Defaults to https://nodejs.org/dist/. Leave [url] blank to use default url.
  		nvm npm_mirror [url]         : Set the npm mirror. Defaults to https://github.com/npm/npm/archive/. Leave [url] blank to default url.
  		nvm uninstall <version>      : The version must be a specific version.
  		nvm use [version] [arch]     : Switch to use the specified version. Optionally specify 32/64bit architecture.
                                 		nvm use <arch> will continue using the selected version, but switch to 32/64 bit mode.
  		nvm root [path]              : Set the directory where nvm should store different versions of node.js.
                                 		If <path> is not set, the current root will be displayed.
  		nvm version                  : Displays the current running version of nvm for Windows. Aliased as v.


6.下载nodejs,命令行输入

	nvm install <版本号>

	例如下载7.6.0版本：nvm install 7.6.0



配置npm
-
配置用户变量--路径是控制面板->系统和安全->系统->高级系统设置->环境变量

注意不要改变系统变量

在用户变量里新建变量名

	（如果下面出现问题检查电脑用户名，如果不是Administrator，把下面Administrator改成相应的电脑用户名）
	变量名  NPM_HOME		变量值  C:\Users\Administrator\node_modules\.bin

psth里面添加如下

	%NPM_Home%;C:\Users\Administrator\AppData\Roaming\npm

命令行输入一下命令测试

	npm -v

结果为

	C:\Users\G>npm -v
	4.1.2


npm淘宝镜像cnp
-

下载命令行输入 如下代码

	npm install -g cnpm --registry=https://registry.npm.taobao.org

下载完成输入如下代码测试

	cnpm -v

显示如下安装成功

	cnpm@4.5.0 (C:\Develop\nvm-noinstall\v7.6.0\node_modules\cnpm\parse_argv.js)
	npm@3.10.10 (C:\Develop\nvm-noinstall\v7.6.0\node_modules\cnpm\node_modules\npm\lib\npm.js)
	node@7.6.0 (C:\Develop\nodejs\node.exe)
	npminstall@2.24.0 (C:\Develop\nvm-noinstall\v7.6.0\node_modules\cnpm\node_modules\npminstall\lib\index.js)
	prefix=C:\Develop\nodejs
	win32 x64 10.0.14393
	registry=https://registry.npm.taobao.org

前端工作环境搭建
-

	1.cnpm install gulp -g --save-dev 
	2.cnpm install gulp --save-dev
	2.gulp -v  检测gulp有没有安装好
	3.到package.json中观察 "devDependencies": {"gulp": "^3.9.1"}  这个代码有,代表配置成功
	4. 到code下 建立一个gulpfile.js 文件
	5. 再建立两个文件  分别命名为src 和 distribution
	6  到src文件夹下 建立 index.html
	7. 将src文件夹下的  index.html 复制到  distribution


以下为以上面为基础建立前端开发自动化
-
环境的搭建需要安装以下功能插件以及安装命令行代码

1.压缩css

	cnpm i gulp-cssnano --save-v
	
2.压缩js

	cnpm i gulp-cancat --save-v

3.压缩html

	cnpm i gulp-htmlmin --save-v

4.混淆js

	cnpm i gulp-uglify --save-v

5.浏览器同步测试

先安装

	cnpm install -g browser-sync

	cnpm i gulp-uglify --save-v

	到src文件夹下 建立 index.html


实现浏览器自动同步需要如下js代码(位于gulpfile.js)

	/**
 	* Created by G on 2017/2/23.
 	*/

	//将index.htmlcopy到distribution
	var gulp = require('gulp');
	gulp.task('copy', function() {
    // 将你的默认的任务代码放在这
    	gulp.src('src/index.html')
	        .pipe(gulp.dest('distribution'))
	        .pipe(browserSync.reload({
	            stream:true
	        }));
	    gulp.src('src/css/*.css')
	        .pipe(gulp.dest('distribution/css'))//把src/css里面css文件copy到distribution/css
		});

	var gulp = require('gulp');
	gulp.task('copyCss', function() {
    // 将你的默认的任务代码放在这
	    gulp.src('src/css/*.css')
	        .pipe(gulp.dest('distribution/css'))//把src/css里面css文件copy到distribution/css
	        .pipe(browserSync.reload({
	        stream:true
		}));
	});


	//less转换成css 并且压缩
	var less = require('gulp-less');
	var cssnano = require('gulp-cssnano');
	gulp.task('less',function () {
    	return gulp.src('src/style/*.less')
        	.pipe(less()) //less转换成css
        	.pipe(cssnano()) //压缩css
        	.pipe(gulp.dest('distribution/css')) //把css文件copy到distribution/css
        	.pipe(gulp.dest('src/css'))//把css文件copy到src/css
        	.pipe(browserSync.reload({
            	stream:true
        	}));
	});


	//压缩js并且混淆js
	var concat = require('gulp-concat');
	var uglify = require('gulp-uglify');
	gulp.task('concat', function() {
    	return gulp.src('src/js/*.js')
        	.pipe(concat('all.js')) //压缩到目标文件
        	.pipe(uglify())//混淆js
        	.pipe(gulp.dest('distribution/js')) //把文件copy到distribution/js
        	.pipe(browserSync.reload({
            	stream:true
        	}))
	});


	//压缩HTML
	var htmlmin = require('gulp-htmlmin');
	gulp.task('minify', function() {
    	return gulp.src('src/*.html')
	        .pipe(htmlmin({collapseWhitespace: true}))
	        .pipe(htmlmin({preserveLineBreaks: true}))
	        .pipe(htmlmin({processConditionalComments: true}))
	        .pipe(htmlmin({removeAttributeQuotes: true}))
	        .pipe(htmlmin({removeComments: true}))
	        .pipe(htmlmin({removeEmptyElements: true}))
	        .pipe(htmlmin({removeScriptTypeAttributes: true}))
	        .pipe(htmlmin({removeStyleLinkTypeAttributes: true}))
	        .pipe(htmlmin({removeAttributeQuotes: true}))
	        .pipe(gulp.dest('distribution'))
	        .pipe(browserSync.reload({
	        stream:true
	    }))
	});
	
	
	//自动监听文件内容变化，实现开发流自动化
	// gulp.task('auto',function () {
	//     gulp.watch('src/css/*.css',['less']);
	//     gulp.watch('src/js/*.js',['concat']);
	//     gulp.watch('src/style/*.less',['less']);
	//     gulp.watch('src/index.html',['minify']);
	// });
	
	
	
	// 代替上面“自动监听文件内容变化，实现开发流自动化”
	var browserSync = require('browser-sync').create();
	// Static server
	gulp.task('serve', function() {
	    browserSync.init({
	        server: {
	            baseDir: "./distribution"
	        }
	    });
	    gulp.watch('src/css/*.css',['less']);
	    gulp.watch('src/js/*.js',['concat']);
	    gulp.watch('src/style/*.less',['less']);
	    gulp.watch('src/css/*.css',['copyCss']);
	    gulp.watch('src/index.html',['minify']);
	});








