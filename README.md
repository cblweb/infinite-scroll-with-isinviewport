# infinite-scroll-with-isinviewport
Simple Infinite Scroll uses jquery plugin [isInViewport](https://github.com/zeusdeux/isInViewport) developed by zeusdeux to trigger load more action by determine the reach of the loading element with the plugin.

## Demo
[http://opensource.cbl-web.com/infinite-scroll-with-isinviewport](http://opensource.cbl-web.com/infinite-scroll-with-isinviewport)

The example uses masonry for beautiful showcase, it is not necessary.

## How it Work
Firstly, we need to listen the scroll event of $(window), by detect the .loadmore element is or is not in-viewport to decide do or not do the *load more action*.
Then we need to check it is during loading or not to prevent more then one *load more action* to be called.
Finally, call your loadMore function.
I would suggest you can save the *load more ajax link*, *the page number* and *the max page number* all in the .loadmore element by data attribute like the demo shown.

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
			el.removeClass('loading'); // Remove the class "loading" from the .loadmore element to allow next load more event
			nextPage++;
			el.data('nextpage',nextPage); // Add up the page number and save back to the data attribute
			if(nextPage>el.data('maxpage')) { // Check if maxpage is reached
				/****** Do something to alert user no more item can load ******/
				el.addClass('nomore'); // This example I add a nomore class to .loadmore element and use css to show that no item is available
				/****** Do something to alert user no more item can load ******/
			}
		});
	},800);
	/*********** Do your ajax requrest to get more item ***********/
};
```

The html .loadmore element:
```
<div class="loadmore" data-nextpage="2" data-url="./ajax/path-to-load-more.html" data-maxpage="5">
  <div class="spinner-wrap">
    <div class="spinner"></div>
  </div>
</div>
```