/*
Enrich Download_Button (from Widget) in the DOM with functionality
See detailed documentation in Dev/mediawiki
deferrable:YES -- This is just an augmentation
*/

(function () {

	// Events

	$('.download-button-v2').each( function() {
		if($(this).attr('data-redirect')) {
			let redirect = $(this).attr('data-redirect').split(',');
			$(this).on( 'click', function() {
				setTimeout( function() {
					window.location = redirect[0];
				}, parseInt( redirect[1] ) );
			});
		}
	});

})();

/*
[[Category:MultiWiki]]
*/