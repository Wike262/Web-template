Анимация поялвения из за экрана при прокрутке.
На блок нужно вставить  js

Названия анимации и примеры с сайта
https://daneden.github.io/animate.css/

// block - селектор анимируемого блока
// nameAnimation - название анимации
// durationAnimate - время проигрывания анимации
// borderShow - сдвиг срабатывания анимации 
	
var windowHeight = $(window).height();
  
showAnimate('#service-animate', 'fadeInLeft', '2s', 20);

function showAnimate(block, nameAnimation, durationAnimate, borderShow) {
	$(document).on('scroll', function() {
		$(block).each(function() {
			var self = $(this),
			height = self.offset().top + borderShow;
			if ($(document).scrollTop() + windowHeight >= height) {
				self.addClass(nameAnimation).css({'animation-duration': durationAnimate})
			}
		});
	});
}