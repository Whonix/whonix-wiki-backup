/*
Make an element with the give class into a button that triggers all expand buttons to either close or open
See detailed documentation in Dev/mediawiki
deferrable:YES -- This is just an augmentation
*/

(function() {

	let allExpanded = false;

	// Setup

	$('.expand-or-collapse-all-button').on('click', function() {
		$('.mw-collapsible-text').each( function() {
			if( allExpanded && $(this).parent().hasClass('mw-collapsible-toggle-expanded')
				|| ! allExpanded && $(this).parent().hasClass('mw-collapsible-toggle-collapsed') ) {
				$(this)[0].click();
			}
		});

		$(this).toggleClass('all-expanded');
		allExpanded = ! allExpanded;
	});

})();

/*
[[Category:MultiWiki]]
*/