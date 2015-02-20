---
title: Managing your application index.html assets with Gulp
date: 2015-01-29 17:01 UTC
tags: order, gulp, environment, index
comments: on
published: false
---

# Managing your application index.html assets with Gulp

There is plenty of posts and resources about how to use [Gulp](http://gulpjs.com/) in order to minify, uglify and concatenate css or js files, but when it comes to **manage things related to development environments** the resources are not that abundant.

The thing I want is easy, when in my **development environment** I want to have my index.html loading all the assets ( js, css, templates, etc.. ) with no minification in order to debug easily everything ( I know about the existence of [source maps](http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/), but I don't like them ):

~~~html
<!DOCTYPE html>
<html>
  <head>

      <link href="css/app-1.css" rel="stylesheet">
      <link href="css/app-2.css" rel="stylesheet">
      <link href="css/app-3.css" rel="stylesheet">
      ...

      <script src="js/app.js"></script>
      <script src="js/app-2.js"></script>
      <script src="js/app-3.js"></script>
      ...

  </head>
  <body>
    mad app
  </body>
</html>
~~~

And when in **production environment** I want my index.html file to only load a minified, uglified and concatenated version of the assets:

~~~html
<!DOCTYPE html>
<html>
  <head>

      <link href="css/app.min.css" rel="stylesheet">
      <script src="js/app.min.js"></script>

  </head>
  <body>
    mad app
  </body>
</html>
~~~

## A simple aproach to accomplish this workflow

Say hello to [Gulp Html Replace](https://www.npmjs.com/package/gulp-html-replace), the gulp addon that allows us to replace some blocks of html.

In order to have my desired workflow I'll create a ```pre-index.html``` that looks quite similar to the development index above mentioned (Note the ```<!--build:xx -->``` blocks ) where all my work will take place:

~~~html
<!DOCTYPE html>
<html>
  <head>

    <!-- build:css -->
      <link href="css/app.css" rel="stylesheet">
      <link href="css/app-2.css" rel="stylesheet">
      <link href="css/app-3.css" rel="stylesheet">
      ...
    <!-- endbuild -->

    <!-- build:js -->
      <script src="js/app.js"></script>
      <script src="js/app-2.js"></script>
      ...
    <!-- endbuild -->

  </head>
  <body>
    mad app
  </body>
</html>
~~~

Then in my gulpfile.js I'll add what I call an ```index``` task, this task will be responsible of building the ```index.html``` file depending on the environment specified.

~~~js
var gulp = require('gulp');
var concat = require('gulp-concat');
var args = require('yargs').argv;
var htmlreplace = require('gulp-html-replace');

var paths = {
  index: ["./app/pre-index.html"],
};

gulp.task('index', function () {

  var env = args.env || 'development';

  if(env == 'production'){

    gulp.src(paths.index)
      .pipe(htmlreplace({
          'css': 'dist/app.min.css',
          'js': 'dist/app.min.js'
      }))
      .pipe(concat('index.html'))
      .pipe(gulp.dest('app/'));

  }else{

    gulp.src(paths.index)
      .pipe(concat('index.html'))
      .pipe(gulp.dest('app/'));

  }

})
~~~

Now I can just do this in order to generate my production index:

~~~sh
gulp index --env production
~~~

Or this in order to generate my development index:

~~~sh
gulp index
~~~

It's a very simple aproach but a very useful one. And of course, you'll need to take care of building the files ```dist/app.min.css``` and ```dist/app.min.js``` but you will easly find plenty of resources about that kind of tasks. You may also want to have a [watch](https://www.npmjs.com/package/gulp-watch) task in order to re-generate your index.html everytime an asset file is added to your pre-index.html, and you could also add your js templates to the mix. 

Another common need is to load some variables **environment variables**, make sure you check [this](http://geekindulgence.com/environment-variables-in-angularjs-and-ionic/) out. The resource is AngularJs based but you could use it wherever you want it.

## A complete gulpfile.js sample

If you are interested in a more complete example here you can see a **gulpfile.js** from one of my projects combining the concepts explained above with some others.

Here's a description of the tasks:

- **replace:** Management of environment variables
- **index:** Generating index.html depending on environment
- **watch:** watcing everything in order to re-generate what it's needed
- **sass:** compiles and builds the css from the sass files
- **templates:** generates one cached views file from a bunch of angular js templates
- **js:** concatenates, minifies and uglifies js files building the app.min.js file
- **styles:** concatenates, minifies and uglifies css files building the app.min.css file

~~~js
var gulp = require('gulp');
var concat = require('gulp-concat');
var sass = require('gulp-sass');
var minifyCss = require('gulp-minify-css');
var rename = require('gulp-rename');
var templateCache = require('gulp-angular-templatecache');
var replace = require('gulp-replace-task');
var args    = require('yargs').argv;
var fs = require('fs');
var uglify = require('gulp-uglify');
var ngAnnotate = require('gulp-ng-annotate');
var htmlreplace = require('gulp-html-replace');

var paths = {
  sass: ['./scss/**/*.scss'],
  templates: [ './www/templates/**/*.html'],
  replaces: ['./config/*.json'],
  index: ["./www/pre-index.html"],
  cssmin: ['./www/css/*.min.css'],
  jsmin:["./www/js/*/*.js"]
};

gulp.task('default', ['sass','templates','replace','index']);

gulp.task('package', ['default','styles', 'js','index']);


// ENVIRONMENT RELATED TASKS

gulp.task('replace', function () {

  var env = args.env || 'development';
  var filename = env + '.json';
  var settings = JSON.parse(fs.readFileSync('./config/' + filename, 'utf8'));

  gulp.src('./www/js/constants.js')
  .pipe(replace({
    patterns: [
      {
        match: 'apiBaseUrl',
        replacement: settings.apiBaseUrl
      }
    ]
  }))
  .pipe(gulp.dest('./www/build/js'));

});

gulp.task('index', function () {
  var env = args.env || 'development';

  if(env == 'production'){

   gulp.src(paths.index)
      .pipe(htmlreplace({
          'css': 'dist/app.min.css',
          'js': 'dist/app.min.js'
      }))
      .pipe(concat('index.html'))
      .pipe(gulp.dest('www/'));

  }else{

   gulp.src(paths.index)
      .pipe(concat('index.html'))
      .pipe(gulp.dest('www/'));

  }

});

// COMMON TASKS

// you can use --env production here
gulp.task('watch', function() {
  gulp.watch(paths.sass, ['sass']);
  gulp.watch(paths.templates, ['templates']);
  gulp.watch(paths.replaces, ['replace']);
  gulp.watch(paths.index, ['index']);
});

gulp.task('sass', function(done) {
  gulp.src('./scss/app.scss')
    .pipe(sass())
    .pipe(gulp.dest('./www/css/'))
    .pipe(minifyCss())
    .pipe(rename({ extname: '.min.css' }))
    .pipe(gulp.dest('./www/css/'))
    .on('end', done);
});

gulp.task('templates', function () {
  gulp.src(paths.templates)
      .pipe(templateCache())
      .pipe(gulp.dest('./www/build/js'));
});

gulp.task('js', function () {
  gulp.src(paths.jsmin)
    .pipe(ngAnnotate())
    .pipe(concat('app.min.js'))
    .pipe(uglify())
    .pipe(gulp.dest('www/dist'))
})

gulp.task('styles', function () {

  gulp.src(paths.cssmin)
    .pipe(concat('app.min.css'))
    .pipe(minifyCss({
      keepSpecialComments: 0
    }))
    .pipe(gulp.dest('www/dist/'))
})

~~~

Hope it's helpful!
