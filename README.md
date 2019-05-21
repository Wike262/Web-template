# Веб шаблон для верстки
В этом шаблоне для верстки используется gulp внутри которого:
* **sass** _Sass - компилятор_
* **browserSync** _Синхронизация с браузером при сохранения файлов_
* **concat** _Библеотека конкатинация_
* **uglify**  _Библеотека для сжатия JS_
* **cssnano** _Библиотека для сжатия CSS_
* **rename** _Библиотека для переименования файлов_
* **del** _Библиотека для удаления файлов_
* **imagemin** _Сжатие изображений_
* **pngquant** _Библеотека для работы с png_
* **cache** _Кеширование_
* **autoprefixer** _Авто-префиксы CSS_
---
Так же подключены такие библиотеки как:
* **Jquery**
* **Jquery-Ui**
* **Bootstrap**
* **Animate-css**
* **Magnific-popup**
* **Clockpicker**
---
## Инструкция
Для работы с этим шаблоном необходимо иметь:
* **[Node.js](https://nodejs.org/en/)**
* **[Gulp](https://gulpjs.com/)**
Для установки всех зависимостей нужно в командной строке ввести:`npm install`
Установить глобально Gulp: `npm install gulp --save-dev -g`
И запустить Gulp введя: `gulp watch`
После этого окружение настроенно и готово к работе. Если ввести команду `gulp build` пройдет компиляция и все файлы будут перемещенны из `/app/` в `/dist/`
## Подробно об окружении
Файл `gupfile.js` представляет из себя следующее:
`var gulp = require('gulp'), // Gulp
    sass = require('gulp-sass'), // Sass
    browserSync = require('browser-sync'), // Browser-Sync
    concat = require('gulp-concat'), // Concat(для конкатенации файлов)
    uglify = require('gulp-uglifyjs'), // Uglify(для сжатия JS)
    cssnano = require('gulp-cssnano'), //CSSnano(сжатие css)
    rename = require('gulp-rename'), //Rename(переменивание файлов)
    del = require('del'), //Del(удаление файлов)
    imagemin = require('gulp-imagemin'), //Сжатие изображений
    pngquant = require('imagemin-pngquant'), //Библеотека для работы с png
    cache = require('gulp-cache'), //Кеширование
    autoprefixer = require('gulp-autoprefixer'); //Авто-префиксы CSS

gulp.task('browser-sync', function (done) { //BrowserSync
    browserSync({
        server: {
            baseDir: 'app'
        },
        notify: false
    });
    browserSync.watch('app').on('change', browserSync.reload);
    done();
});

gulp.task('clean', function () { //Очищает dist
    return del(['dist']);
});

gulp.task('sass', function () { //Компиляция SASS
    return gulp.src('app/sass/**/*.sass')
        .pipe(sass())
        .pipe(autoprefixer(['last 15 versions', '> 1%', 'ie 8', 'ie 7'], {
            cascade: true
        }))
        .pipe(gulp.dest('app/css'))
        .pipe(browserSync.reload({
            stream: true
        }))
});

gulp.task('css-libs', gulp.series('sass', function () { //Сжатие CSS
    return gulp.src('app/css/libs.css')
        .pipe(cssnano())
        .pipe(rename({
            suffix: '.min'
        }))
        .pipe(gulp.dest('app/css'));
}));

gulp.task('scripts', function () { //Подключение и сжатие JS
    return gulp.src([
            'app/libs/jquery/jquery.min.js', //Jquery
            'app/libs/jquery_ui/jquery-ui.min.js', //Jquery-ui
            'app/libs/clockpicker-gh-pages/jquery-clockpicker.min.js', //Clockpicker
            'app/libs/magnific-popup/jquery.magnific-popup.min.js', //Magnific-popup
            'app/libs/bootstrap/js/bootstrap.min.js', //Bootstrap
        ])
        .pipe(concat('libs.min.js'))
        .pipe(uglify())
        .pipe(gulp.dest('app/js'));
});

gulp.task('img', function () { //Сжатие изображений
    return gulp.src('app/img/**/*')
        .pipe(cache(imagemin({
            interlaced: true,
            progressive: true,
            svgoPlugins: [{
                removeViewBox: false
            }],
            use: [pngquant()]
        })))
        .pipe(gulp.dest('dist/img'))
});

gulp.task('watch', gulp.series('browser-sync', 'css-libs', 'scripts', function (done) { //Компиляция в браузер
    gulp.watch('app/sass/**/*.sass', gulp.parallel('sass')); //SASS
    gulp.watch('app/*.html'); //HTML
    gulp.watch('app/js/**/*.js'); //JS
    done();
}));

gulp.task('build', gulp.series('clean', 'img', 'sass', 'scripts', function (done) { //Компиляция в продакшен
    var buildCss = gulp.src([ //CSS
            'app/css/*.css',
        ])
        .pipe(gulp.dest('dist/css'))
    var buildFonts = gulp.src('app/fonts/**/*') //Fonts
        .pipe(gulp.dest('dist/fonts'))
    var buildJs = gulp.src('app/js/**/*') //JS
        .pipe(gulp.dest('dist/js'))
    var buildHtml = gulp.src('app/*.html') //HTML
        .pipe(gulp.dest('dist'));
    done();
}));

gulp.task('clear', function (callback) { //Очистка кэша
    return cache.clearAll();
})

gulp.task('default', gulp.parallel('watch'));` 
## Подробно об библиотеках