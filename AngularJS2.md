# Angular 2.0 for mobile

* [Angular-cli](http://blog.csdn.net/alabadazi/article/details/53288885)

```
npm install -g angular-cli
ng --help
ng new Project_Name
cd Project_Name
ng serve
ng serve --host 127.0.0.0 --port 4200 --live-reload-port 49153

生成组件、指令、管道以及服务（ng generate or ng g）
ng generate component my-new-component
ng g component new-cmp
ng g component ../newer-cmp

可被构建的组件类型
Scaffold            Usage
Component           ng g component my-new-component
Directive           ng g directive my-new-directive
Pipe                ng g pipe my-new-pipe
Service             ng g service my-new-service
Class               ng g class my-new-class
Interface           ng g interface my-new-interface
Enum                ng g enum my-new-enum
Module              ng g module my-module

编译
ng build

ng build 可以指定两种编译目标： (--target=production or --target=development) 和 (--environment=dev or --environment=prod)
```













# AngularJS 2.0 Start

* 2.0版本 基于 ES6 ，由于很多浏览器还不支持ES6，所以Angular2引入了很多polyfill或者shim, 导致我们引入了第三方依赖

> package.json

```
{
  "name": "angular2-seed",
  "version": "0.2.0",
  "description": "",
  "main": "index.js",
  "author": "flyingzl(flyingzl@gmail.com)",
  "repository": {
    "type": "git",
    "url":  "https://github.com/flyingzl/angular2-seed"
  },
  "license": "MIT",
  "devDependencies": {
    "del": "~1.1.1",
    "gulp": "~3.8.10",
    "gulp-connect": "^2.2.0",
    "gulp-plumber": "^1.0.0",
    "gulp-open": "^0.3.2",
    "gulp-rename": "~1.2.0",
    "gulp-size": "^1.2.1",
    "gulp-traceur": "0.17.*",
    "systemjs-builder": "^0.10.3",
    "through2": "~0.6.3"
  },
  
  "dependencies": {
    "angular2": "2.0.0-alpha.25",
    "es6-module-loader": "0.16.5",
    "systemjs": "0.16.7",
    "reflect-metadata": "0.1.0"
  }
}
```

> gulpfile.js

```
'use strict';

var gulp = require('gulp'),
    del = require('del'),
    plumber = require('gulp-plumber'),
    rename = require('gulp-rename'),
    traceur = require('gulp-traceur');


var connect = require('gulp-connect'),
    open = require('gulp-open'),
    port = 3456;

var PATHS = {
    src: {
        js: 'src/**/*.js',
        html: 'src/**/*.html'
    },
    lib: [
        'node_modules/gulp-traceur/node_modules/traceur/bin/traceur-runtime.js',
        'node_modules/es6-module-loader/dist/es6-module-loader-sans-promises.src.js',
        'node_modules/systemjs/lib/extension-register.js',
        'node_modules/angular2/node_modules/zone.js/dist/zone.js',
        'node_modules/angular2/node_modules/zone.js/dist/long-stack-trace-zone.js',
        'node_modules/reflect-metadata/Reflect.js',
        'node_modules/reflect-metadata/Reflect.js.map',
    ]
};


gulp.task('watch', function() {
    gulp.watch(PATHS.src.js, ['js']);
    gulp.watch(PATHS.src.html, ['html']);
});

gulp.task('js', function() {
    return gulp.src(PATHS.src.js)
        .pipe(rename({
            extname: ''
        })) //hack, see: https://github.com/sindresorhus/gulp-traceur/issues/54
        .pipe(plumber())
        .pipe(traceur({
            modules: 'instantiate',
            moduleName: true,
            annotations: true,
            types: true,
            memberVariables: true
        }))
        .pipe(rename({
            extname: '.js'
        })) //hack, see: https://github.com/sindresorhus/gulp-traceur/issues/54
        .pipe(gulp.dest('dist'));
});

gulp.task('html', function() {
    return gulp.src(PATHS.src.html)
        .pipe(gulp.dest('dist'));
});

gulp.task('angular2', function() {

    var buildConfig = {
        paths: {
            "angular2/*": "node_modules/angular2/es6/prod/*.es6",
            "rx": "node_modules/angular2/node_modules/rx/dist/rx.js"
        },
        meta: {
            // auto-detection fails to detect properly here - https://github.com/systemjs/builder/issues/123
            'rx': {
                format: 'cjs'
            }
        }
    };

    var Builder = require('systemjs-builder');
    var builder = new Builder(buildConfig);

    return builder.build('angular2/angular2', 'dist/lib/angular2.js', {});
});

gulp.task('libs', ['angular2'], function() {
    var size = require('gulp-size');
    return gulp.src(PATHS.lib)
        .pipe(size({
            showFiles: true,
            gzip: true
        }))
        .pipe(gulp.dest('dist/lib'));
});



gulp.task('connect', function() {
    connect.server({
        root: __dirname + '/dist',
        port: port,
        livereload: true
    });
});

gulp.task('open', function() {
    var options = {
        url: 'http://localhost:' + port,
    };
    gulp.src('./index.html')
        .pipe(open('', options));
});

gulp.task('build', ['js', 'html'])
gulp.task('default', ['build', 'libs']);
gulp.task('serve', ['connect', 'open']);
gulp.task('clean', function(done) {
    del(['dist'], done);
});
```

> 案例

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Angular2 Hello world</title>
</head>

<body>
    
    <sample-app>正在加载中...</sample-app>

    <!-- ES6-related imports -->
    <script src="lib/traceur-runtime.js"></script>
    <script src="lib/es6-module-loader-sans-promises.src.js"></script>
    <script src="lib/extension-register.js"></script>
    <script src="lib/reflect.js"></script>
    <script>
    register(System);
    </script>

    <!-- Angular2-related imports -->
    <script src="lib/zone.js"></script>
    <script src="lib/long-stack-trace-zone.js"></script>
    <script src="lib/angular2.js"></script>

    <!-- Application bootstrap logic -->
    <script>
    System.import('app')
        .then(function(app) {
            app.main();
        }, function(e) {
            console.error('Boostrap angular2 failed!', e);
        });
    </script>
</body>

</html>
```