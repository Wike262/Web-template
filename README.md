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
* **[Jquery](https://jquery.com/)**
* **[Jquery-Ui](https://jqueryui.com/)**
* **[Bootstrap](https://bootstrap-4.ru/docs/4.3.1/getting-started/introduction/)**
* **[Animate-css](https://daneden.github.io/animate.css/)**
* **[Magnific-popup](https://dimsemenov.com/plugins/magnific-popup/)**
* **[Clockpicker](https://weareoutman.github.io/clockpicker/)**
---
## Инструкция
Для работы с этим шаблоном необходимо иметь:
* **[Node.js](https://nodejs.org/en/)**
* **[Gulp](https://gulpjs.com/)**
Для установки всех зависимостей нужно в командной строке ввести:
```cmd
npm install
```
Установить глобально Gulp: 
```cmd
npm install gulp --save-dev -g
```
И запустить Gulp введя:
```cmd
gulp watch
```
После этого окружение настроенно и готово к работе. Если ввести команду 
```cmd
gulp build
```
пройдет компиляция и все файлы будут перемещенны из `/app/` в `/dist/`
## Подробно об окружении
Файл `gupfile.js` представляет из себя следующее:
```js
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    browserSync = require('browser-sync'),
    concat = require('gulp-concat'),
    uglify = require('gulp-uglifyjs'),
    cssnano = require('gulp-cssnano'),
    rename = require('gulp-rename'),
    del = require('del'),
    imagemin = require('gulp-imagemin'),
    pngquant = require('imagemin-pngquant'), 
    cache = require('gulp-cache'),
    autoprefixer = require('gulp-autoprefixer');

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

gulp.task('watch', gulp.series('browser-sync', 'css-libs', 'scripts', function (done) { //Синхронизация с браузером
    gulp.watch('app/sass/**/*.sass', gulp.parallel('sass')); //SASS
    gulp.watch('app/*.html'); //HTML
    gulp.watch('app/js/**/*.js'); //JS
    done();
}));

gulp.task('build', gulp.series('clean', 'img', 'sass', 'scripts', function (done) { //Компиляция
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

gulp.task('default', gulp.parallel('watch'));
```
## Подробно об библиотеках
* **[Jquery](https://jquery.com/)**
 >**Jquery** - это быстрая, небольшая и многофункциональная библиотека JavaScript. Это делает такие вещи, как прохождение и манипулирование документами HTML, обработку событий, анимацию и Ajax намного проще с помощью простого в использовании API, который работает во множестве браузеров. Благодаря сочетанию универсальности и расширяемости, jQuery изменил способ, которым миллионы людей пишут JavaScript.
---
* **[Jquery-Ui](https://jqueryui.com/)**
 >Пользовательский интерфейс jQuery представляет собой набор взаимодействий, эффектов, виджетов и тем пользовательского интерфейса, созданный на основе библиотеки JavaScript jQuery. JQuery UI - идеальный выбор для создания веб-приложений с высокой степенью интерактивности или просто для добавления средства выбора даты в элемент управления формы.
---
* **[Bootstrap](https://bootstrap-4.ru/docs/4.3.1/getting-started/introduction/)**
 >**Bootstrap** - это инструментарий с открытым исходным кодом для разработки с помощью HTML, CSS и JS. Используйте переменные Sass и миксины, гибкую систему сеток, множество готовых компонентов и мощных плагинов, основанных на jQuery.
---
* **[Animate-css](https://daneden.github.io/animate.css/)**
 >**Animate-css** - очень гибкая библиотека js для плавного появления блоков.
---
* **[Clockpicker](https://weareoutman.github.io/clockpicker/)**
 >**Clockpicker** - библеотека для красивой анимации выбора часов