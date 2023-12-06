/*
Adds a PayPal payment module to the given DOM elements
See detailed documentation in Dev/mediawiki
deferrable:YES -- This script needs a little bit to load anyways
*/

(function() {

	// 0. Data

	let standards = {
		minimumDollars: 5, // in full dollars
		presetDollars: {
			once: [20, 50, 200, 500, 1000],
			monthly: [10, 20, 50, 100, 200],
			yearly: [20, 50, 100, 200, 500],
		}
	};

	let intervalModes = {
		once: '_xclick',
		subscription: '_xclick-subscriptions',
	};

	// 1. Elements

	let template = $(`
    <div class="interval">
      <div class="options">
        <span class="once">Once</span>
        <span class="monthly">Monthly</span>
        <span class="yearly">Yearly</span>
      </div>
      <div class="error">Please select an interval</div>
    </div>

    <div class="amount">
      <div class="options">
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        <span></span>
        <span class="manual">
          <input id="input-amount" type="text" autocomplete="off" />
        </span>
      </div>

      <div class="interval-hint">
        <span>➥</span>
        Please select interval
      </div>

      <div class="error">
        Please select an amount of min
        $<span class="min"></span>
      </div>
    </div>

    <form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_blank">
      <input type="hidden" name="business" value="patrick_schleizer@web.de">
      <input type="hidden" name="currency_code" value="USD">
      <input type="hidden" name="item_name" value="Plus Support">
      <input type="hidden" name="cmd">
      <input type="hidden" name="no_note" value="1">
      <button type="submit" class="submit">
        DONATE
        <span>via PayPal</span>
    </form>
  `);

	let elementTemplates = {
		// Time parameters. Syntax: p3 times t3, e.g. p3=6&t3=M -> 6 months, p3=1&t3=Y -> 1 year
		interval: {
			monthly: $('<input type="hidden" name="p3" value="1"><input type="hidden" name="t3" value="M"><input type="hidden" name="src" value="1">'),
			yearly: $('<input type="hidden" name="p3" value="1"><input type="hidden" name="t3" value="Y"><input type="hidden" name="src" value="1">'),
		},
		amount: {
			once: $('<input type="hidden" name="amount">'),
			subscription: $('<input type="hidden" name="a3">'),
		},
	};

	// 2. Methods

	function isCurrentAmountValid(amount) {
		let parsedAmount = Number.parseFloat(amount);
		return typeof parsedAmount == 'number' && Number.isNaN(parsedAmount) == false && parsedAmount >= standards.minimumDollars;
	}

	function fillAmountsByInterval(root, interval) {
		root.find('.amount span').each((index, spanAmount) => {
			$(spanAmount).text(standards.presetDollars[interval][index]);
		});
	}

	// 3. jQuery Extension

	$.fn.payViaPaypal = function(action) {

		// Only allow 'init' at the moment (extendable later)
		if (action != 'init') return;

		$(this).each(function() {
			// Prevent double initialization
			if ($(this).hasClass('js-fully-loaded')) return;

			let amount;

			$(this).html(template.clone());

			let formElements = {
				interval: {
					monthly: elementTemplates.interval.monthly.clone(),
					yearly: elementTemplates.interval.yearly.clone()
				},
				amount: {
					once: elementTemplates.amount.once.clone(),
					subscription: elementTemplates.amount.subscription.clone()
				},
			};

			let root = $(this);
			let form = root.children('form');
			let inputCmd = form.children('input[name=cmd]');

			// 4. Events

			root.find('.amount span').on('click', function() {
				if (!inputCmd.val()) {
					root.addClass('paypal-interval-error');
					return;
				}

				$(this).siblings().removeClass('active');
				$(this).addClass('active');

				if ($(this).hasClass('manual')) {
					amount = $(this).children('input').val();
					$(this).children('input')[0].focus();
				} else {
					amount = $(this).text();
				}

				if (isCurrentAmountValid(amount)) root.removeClass('paypal-amount-error');
			});

			root.find('.amount span input').on('input', function() {
				amount = $(this).val();

				if (isCurrentAmountValid(amount)) root.removeClass('paypal-amount-error');
			});

			root.find('.interval span').on('click', function() {
				if (!root.hasClass('interval-selected')) {
					let amountEl = root.find('.amount');
					let heightBefore = amountEl.outerHeight();
					root.addClass('interval-selected');
					let heightAfter = amountEl.outerHeight();
					amountEl.css('height', heightBefore).animate({
						height: heightAfter + 'px'
					}, 200, () => amountEl.css('height', ''));
				}

				let t = $(this).text().toLowerCase();

				fillAmountsByInterval(root, t.toLowerCase());

				// Remove selected amount if NOT manual
				if (!root.find('.amount span.active').hasClass('manual')) {
					root.find('.amount span').removeClass('active');
					root.find('.amount span.manual input').val('');
					amount = undefined;
				}

				$(this).siblings().removeClass('active');
				$(this).addClass('active');

				// Clear previous interval and amount inputs
				form.find('input[name=p3], input[name=t3], input[name=a3]').remove();

				if (t === 'once') {
					formElements.interval.monthly.remove();
					formElements.interval.yearly.remove();
					formElements.amount.subscription.remove();
					form.prepend(formElements.amount.once);
					inputCmd.val(intervalModes.once);
				} else {
					formElements.amount.once.remove();
					form.prepend(formElements.amount.subscription);

					if (t === 'monthly') {
						formElements.interval.yearly.remove();
						form.prepend(formElements.interval.monthly);
					} else if (t === 'yearly') {
						formElements.interval.monthly.remove();
						form.prepend(formElements.interval.yearly);
					}

					inputCmd.val(intervalModes.subscription);
				}

				root.removeClass('paypal-interval-error');
			});

			form.on('submit', function() {
				if (!inputCmd.val()) {
					root.addClass('paypal-interval-error');
					return false;
				}

				if (isCurrentAmountValid(amount) == false) {
					root.addClass('paypal-amount-error');
					return false;
				}

				if (inputCmd.val() === intervalModes.subscription) {
					let subscriptionAmount = Number.parseFloat(amount).toFixed(2);
					console.log("Subscription amount set:", subscriptionAmount);
					form.children('input[name=a3]').val(subscriptionAmount);
					console.log("Subscription amount ok.");
				} else {
					form.children('input[name=amount]').val(Number.parseFloat(amount).toFixed(2));
				}

				// Debug output before setting the form input values
				console.log("Form Submission Details:");
				console.log("Amount before parsing: " + amount);
				console.log("Parsed amount: " + Number.parseFloat(amount).toFixed(2));
				$(this).find('input').each(function() {
					console.log($(this).attr('name') + ': ' + $(this).val());
				});

				var formData = $(this).serialize();
				console.log("Form Data: ", formData);
				console.log("Submission URL: ", this.action + "?" + formData);

				return true;
			});

			// 6. Finalize Setup

			fillAmountsByInterval(root, 'once');
			root.find('.amount .error .min').text(standards.minimumDollars);

			root.addClass('js-fully-loaded');
			root.animate({
				opacity: 1
			});
		});

	};

	// 4. Auto-Initialization

	$('.pay-via-paypal-module').payViaPaypal('init');

})();

/*
[[Category:MultiWiki]]
*/