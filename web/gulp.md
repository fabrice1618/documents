# gulp

- https://gulpjs.com/
- Open source javascript task runner
- Front-end build system
- Build on node.js et npm
- Used for time consuming and repetitive tasks
- Plugins for every tasks


## example tasks

- Concatenation & minification of javascript & CSS
- image optimization
- cache busting
- testing, linting
- development webserver

## How gulp works

- Built on node streams
- pipelines: .pipe() operator
- single purpose plugins
- sync / async processing: series()/parallel()

## global installation

```bash
gulp -v
npm rm --global gulp

npm install --global gulp-cli
gulp -v
[23:06:31] CLI version 2.0.1
[23:06:31] Local version 3.9.
```

## project installation

```bash
npm install gulp --save-dev
npm install gulp-sass gulp-clean-css gulp-rename --save-dev
```

## gulpfile.js

```javascript
var gulp = require('gulp');
var sass = require('gulp-sass');
var cleanCSS = require('gulp-clean-css');
var rename = require('gulp-rename');
gulp.task('sass', function () {
    return gulp.src('./assets/scss/landio.scss')
        .pipe(sass()).pipe(cleanCSS())
        .pipe(rename({suffix: '.min'}))
        .pipe(gulp.dest('./assets/css'))
    }
); 

gulp.task('default', ['sass']);
```

## Preprocessing sass

```javascript
    gulp.task('sass', function () {
        return gulp.src('./assets/scss/landio.scss')
            .pipe(sourcemaps.init())
            .pipe(sass())
            .pipe(autoprefixer({
                browsers: [
                    "Explorer >= 10",
                    "iOS >= 9.3", // Apple iPhone 5
                    "Android >= 5"
                    ]
            }))
            .pipe(cleanCSS())
            .pipe(rename({suffix: '.min'}))
            .pipe(sourcemaps.write('.'))
            .pipe(gulp.dest('./assets/css'))
        });
```

## processing javascript

```javascript
    gulp.task('js', function() {
        return gulp.src('./assets/js/landio.js')
        .pipe(sourcemaps.init())
        .pipe(include())
        .on('error', console.log)
        .pipe(uglify())
        .pipe(rename({suffix: '.min'}))
        .pipe(sourcemaps.write('.'))
        .pipe(gulp.dest('./assets/js/'))
    });
```

## gulp watch

```javascript
gulp.watch('./assets/**/*.scss', ['sass']);
gulp.watch('./assets/js/landio.js', ['js']);
```

## webserver

```javascript
gulp.task('serve', ['sass', 'js'], function() {
    browserSync.init({
        port: 3355,
        server: {
            baseDir: "./",
            index: "index.html",
            logSnippet: false
        } 
    });
```

# browser sync

```javascript
gulp.task('sass', function () {
    // ...
    .pipe(gulp.dest('./assets/css'))
    .pipe(browserSync.stream());
    });
gulp.task('js', function() {
    // ...
    .pipe(gulp.dest('./assets/js/'))
    .pipe(browserSync.stream());
    });
gulp.task('serve', ['sass', 'js'], function() {
    // ...
    gulp.watch('./assets/**/*.scss', ['sass']);
    gulp.watch('./assets/js/landio.js', ['js']);
    gulp.watch('./*.html').on('change', reload);}); 
gulp.task('default', ['serve']);
```

## sync / async

```javascript
const { series, parallel } = require('gulp');
function clean(cb) {
    // body omitted
    cb();
} 
function css(cb) {
    // body omitted
    cb();
} 
function javascript(cb) {
    // body omitted
    cb();
} 
exports.build = series(clean, parallel(css, javascript));
```

