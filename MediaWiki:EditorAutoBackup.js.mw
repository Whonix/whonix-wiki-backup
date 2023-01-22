/*
EditorAutoBackup Gadget - CSS styling. The editor can now save a backup of the last save to localStorage
See detailed documentation in Dev/mediawiki
deferrable:YES -- This is just an augmentation
*/

(function () {

	if( $('#editform').length ) setTimeout( function() {

		let allowSubmit = false;

		// Setup

		var editor;
		if( $('#editform .ace_editor' ).length ) {
			$('#editform .ace_editor' ).attr('id','editform-ace-editor');
			editor = ace.edit('editform-ace-editor');
		}

		$('#wikiEditor-ui-toolbar').append($( '<i class="editor-auto-backup far fa-save"></i>' ));

		let modal = $( ''
			+ '<div class="mini-modal" id="editorAutoBackupModal">'
			+ ' <div class="info">Your latest AutoBackup</div>'
			+ ' <div class="title"></div>'
			+ ' <div class="date"></div>'
			+  '<textarea></textarea>'
			+ '</div>'
		);

		$('body').append(modal);

		// Events

		function saveBackup( alwaysPlain ) {
			let content = editor && !alwaysPlain ? ace.edit('ace_editor').getValue() : $('#wpTextbox1').val();
			localStorage.setItem('wikiEditorAutoBackup', JSON.stringify({
				content: content,
				date: (new Date()).toLocaleString(),
				title: $('#firstHeading').text(),
			}));
		}

		$('.save-and-continue').on( 'click', saveBackup );

		$('#editform').on( 'submit', function(e) {
			if( allowSubmit ) return true;

			e.preventDefault();

			let submitter = document.activeElement;

			saveBackup( true );

			setTimeout( function() {
				allowSubmit = true;
				$('#editform')[0].requestSubmit( submitter );
			}, 100 );

			return false;
		});

		$('#wikiEditor-ui-toolbar .editor-auto-backup').on( 'click', function() {
			let data = JSON.parse( localStorage.getItem('wikiEditorAutoBackup') );
			modal.find('.title').text(data.title);
			modal.find('.date').text(data.date);
			modal.find('textarea').val(data.content);
			$('#editorAutoBackupModal').miniModal('show');
		});

	}, 500 );

})();

/*
[[Category:MultiWiki]]
*/