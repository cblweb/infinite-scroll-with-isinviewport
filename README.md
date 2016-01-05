# infinite-scroll-with-isinviewport
Simple Infinite Scroll uses jquery plugin [isInViewport](https://github.com/zeusdeux/isInViewport) developed by zeusdeux to trigger load more action by determine the reach of the loading element with the plugin.

## Demo
[http://opensource.cbl-web.com/infinite-scroll-with-isinviewport](http://opensource.cbl-web.com/infinite-scroll-with-isinviewport)

The example uses masonry for beautiful showcase, it is not necessary.

## How it Work
Firstly, we need to listen the a scroll event of $(window), by detect the .loadmore element is or is not in-viewport to decide do or not do the load more action. Then we need to check it is during loading or not to prevent more then one load more action to be called.

```javascript
$container = $('#item-list');
$container.masonry();

$(window).scroll(function() {
	if(!$('.loadmore').hasClass('nomore') && $('.loadmore').is(":in-viewport")) {
		if(!$('.loadmore').hasClass('loading')) {
			loadMore($('.loadmore'));
		};
	};
});

function loadMore(el) {
	var nextPage = el.data('nextpage');
	el.addClass('loading');
	/*********** Do your ajax requrest to get more item ***********/
	setTimeout(function() { // Here I set a delay to show the loading effect more clear, you can remove this from your production
		$.post(el.data('url'),{page:nextPage},function(data) {
			$items = $(data); // Assume data is the html body of the items
			$container.append($items);
			$container.masonry('appended',$items);
			el.removeClass('loading');
			nextPage++;
			el.data('nextpage',nextPage);
			if(nextPage>el.data('maxpage')) {
				el.addClass('nomore');
				/****** Do something to alert user no more item can load ******/
				el.hide();
				/****** Do something to alert user no more item can load ******/
			}
		});
	},800);
	/*********** Do your ajax requrest to get more item ***********/
};
```

I would suggest you can save the load more ajax link, the page number and the max page number in the loadmore element by data attr like the demo shown.

```
<div class="loadmore" data-nextpage="2" data-url="./ajax/path-to-load-more.html" data-maxpage="5">
  <div class="spinner-wrap">
    <div class="spinner"></div>
  </div>
</div>
```
