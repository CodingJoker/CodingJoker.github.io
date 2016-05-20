---
title: 转战gulp，实现前端静态资源的压缩，加MD5戳，替换引用，加CDN前缀
id: 303
categories:
  - 前端小记
date: 2016-03-27 11:08:40
tags:
---
<Excerpt in index | 首页摘要>
+ 为什么转战Gulp
+ Gulp前端项目打包
<!-- more -->
<The rest of contents | 余下全文>

之前一直在使用grunt，总感觉grunt的参数配置方法很繁琐，没有一个比较清晰的流程，加上自己对gulp的流操作比较感兴趣，就决定转战gulp了。

相信前端er在发布自己的代码时如果不使用前端一些构建工具的话，对频繁上线的项目来说，自己手动压缩打包替换CDN前缀那是一件多么恐怖的事情！所以采用gulp前端构建工具是一个非常明智的选择。

>首先来说说发布项目时前端需要进行哪些操作：

	1.对静态资源的压缩合并
	2.对js,css，img等资源加MD5戳
	3.对引用了以上加MD5戳的HTML文件进行替换文件内的引用。
	4.为HTML文件内的引用加CDN前缀

>虽然看起来就这4步，不过写起任务来还是挺麻烦的,上代码：

```
  var gulp = require('gulp');
  var minimist = require('minimist');
  var uglify = require('gulp-uglify');
  var minifyHtml = require('gulp-minify-html');
  var minifyCss = require('gulp-minify-css');
  var rev = require('gulp-rev');
  var revReplace = require('gulp-rev-replace');
  var prefix = require('gulp-prefix');
  var zip = require('gulp-zip');
  var gulpSequence = require('gulp-sequence');
  var cmd = {
  string: 'v',
  default: { v: '1.0' }
  };
  var option = {
  src:'poster',
  dest:'poster/build',
  cdn:'http://sns_wf.cdn.sohusce.com'///poster/
  }
  var options = minimist(process.argv.slice(2), cmd);
  var version = options.v;
  //统一加MD5之后替换引用
  gulp.task('cdn',function(){
  var revAll = new RevAll({dontRenameFile:[/^\/.*.html/]});// ,/^\/.*.jpg|png/
  gulp.src([option.src+'/templates-dev/*.html',option.src+'/static/**'])
  .pipe(revAll.revision())
  .pipe(gulp.dest(option.dest+'/'+version))
  .pipe(revAll.versionFile())
  .pipe(gulp.dest(option.dest+'/'+version))
  .pipe(revAll.manifestFile())
  .pipe(gulp.dest(option.dest+'/'+version)); 
  });
  gulp.task("rep" ,function(){
  var manifest = gulp.src(option.dest + '/'+ version + "/rev-manifest.json");
  console.log(option.dest +'/'+ version);
  return gulp.src(option.dest +'/'+ version + '/templates-dev/*.html')
  .pipe(revReplace({manifest: manifest}))
  .pipe(gulp.dest(option.src +'/templates' ));
  });
  gulp.task('prefix',function(){
  console.log('加CDN前缀...');
  return gulp.src(option.src+'/templates/*.html')
  .pipe(prefix(option.cdn, null))
  .pipe(gulp.dest(option.src+'/templates/'));
 })
  gulp.task('htmlmin',function(){
  return gulp.src([option.src + '/templates/*.html'],{base:option.src })
  .pipe(minifyHtml())
  .pipe(gulp.dest(option.src));
  })
  gulp.task('jsmin',function(){
  return gulp.src([option.dest +'/'+ version + '/static/js/*.js',option.dest +'/'+ version + '/static/imgcut/js/*.js'],{base:option.dest +'/'+ version})
  .pipe(uglify())
  .pipe(gulp.dest(option.dest +'/'+ version));
  })
  gulp.task('cssmin',function(){
  return gulp.src([option.dest +'/'+ version + '/static/css/*.css',option.dest +'/'+ version + '/static/imgcut/css/*.css'],{base:option.dest +'/'+ version})
.pipe(minifyCss())
.pipe(gulp.dest(option.dest +'/'+ version));
})
gulp.task('zip',function(){
console.log('压缩中...')
return gulp.src([option.dest+'/'+version+'/static/**',])
.pipe(zip('static.zip'))
.pipe(gulp.dest(option.dest+'/'+version));
})
gulp.task('r',function(cb){
gulpSequence('rep','prefix',['htmlmin','jsmin','cssmin'],'zip',cb)
})
```

> 这里引用的插件名可以去npm官网上搜下作用和使用方法，这里不再详细叙述，主要讲下几个关键的插件：

+ **[gulp-rev-all](https://www.npmjs.com/package/gulp-rev-all "gulp-rev-all")  给静态资源加MD5戳，生成源文件名和打了戳的MD5文件名的一个rev-manifest.json文件（这很关键）。**
+ **[gulp-prefix](https://www.npmjs.com/package/gulp-prefix "gulp-prefix") 给引用了静态资源的的HTML文件替换引用和加CDN前缀**
+ **[minimist](https://www.npmjs.com/package/minimist "minimist") 给任务的执行添加参数 eg: gulp r --v 1.0 **
+ **[gulp-sequence](https://www.npmjs.com/package/gulp-sequence "gulp-sequence") 将任务的执行按指定的顺序执行**

> 说完了一些重要的插件，咱再来讲讲咱得任务。


+ **任务cdn 详见插件介绍的gulp-rev-all 。**
+ **任务rep 详见gulp-prefix**
+ **任务htmlmin,cssmin,jsmin,zip 给静态资源压缩后打成一个zip包，方便部署CDN。**
+ **任务r 组织任务的执行，防止子任务执行中的异步操作扰乱整个任务的执行。**

> 这里R任务有个缺点就是没讲CDN任务放在其中，因为CDN任务中包含了多个异步任务，是的在R任务中产生了同步任务的假象，导致后续的操作和产生的结果错误。所以在终端需要执行以下两步任务：

+ **gulp cdn --v 1.0  //生成1.0版本**
+ **gulp r**
+ **一些配置参数写在了option对象中，同学们可以按照自己的需求来修改。其中dest是任务输出目录，打包好的zip文件在bulid/x.x 目录下。**

> 介绍完毕~

> 快来跟我转战gulp吧~ 