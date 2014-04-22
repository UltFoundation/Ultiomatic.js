UltStyle.js
===========

Ult Foundation's simple &amp; effective JavaScript coding style guidelines.

This style guide is largely based on [Idiomatic.js](https://github.com/rwaldron/idiomatic.js/#idiomatic-style-manifesto) and most if not all of its rules apply, except otherwise explicitly mentioned. Go read it if you haven't already. `:)`


## Purpose

Achieve a decent level of coding style consistency in [@UltFoundation](https://github.com/UltFoundation) and [@UltCombo](https://github.com/UltCombo)'s projects, serving as a simple style guide for contributors.

Consistency is very important, but it shouldn't get in the way of development. Hence, this style guide will be relatively short and more permissive than Idiomatic.js.

## Editor configs

These are basic configurations which should be handled at IDE/text editor level.

- Character set: **UTF-8**
	- One encoding to rule them all.
- Indent style: **tab**
	- [**Tabs for indentation, spaces for alignment.**](http://www.emacswiki.org/SmartTabs) See also: [Why tabs are clearly superior](http://lea.verou.me/2012/01/why-tabs-are-clearly-superior/).
- **Trim trailing whitespace**
	- [**TRAILING WHITESPACE IS EVIL.**](http://codeimpossible.com/2012/04/02/Trailing-whitespace-is-evil-Don-t-commit-evil-into-your-repo-/) See also: [Why is trailing whitespace a big deal? - Programmers.SE](http://programmers.stackexchange.com/q/121555/91517). There are countless resources about this if you want to search.
	- **Note:** trailing whitespace is meaningful in Markdown, however developers should prefer paragraph breaks over line breaks, and use explicit `<br>`s when line breaks are absolutely necessary. Visible markup makes the intent much more clear than pesky trailing whitespace.
- **Ensure newline at end of file**
	- Avoids the "No newline at end of file" Git warning when viewing diffs. Minified files are an exception to this rule.
- Line endings: **LF**
	- Even in Windows, any decent editor can handle Unix-style line endings. In your development environment, this setting is not really meaningful for most projects. However, for developers to work consistently in different development platforms, you must ensure that committed source files have LF line endings:<br>
`git config --global core.autocrfl input`<br>
Or, `cd` into the desired repository and run the command without the `--global` option if you don't want to change your global Git settings.<br>
**Note:** having `core.autocrfl` set to `true` in Windows platforms is also acceptable in most projects, but `input` is preferred over `true` so that development environments have consistent line endings independent of the platform.

An [EditorConfig](http://editorconfig.org/) file with these settings is available [here](https://github.com/UltFoundation/config_files/.editorconfig). Make sure your Git client's `autocrlf` setting is set to a suitable value for the projects being worked on.

## Syntax

Virtually all of [Idiomatic.js](https://github.com/rwaldron/idiomatic.js) principles apply. The notable divergences and exceptions are:

- Generally avoid inner whitespace in parentheses and square brackets. Inner whitespace may be used to aid in reading nested parentheses:

	```js
	if (findOriginOfLife) {
		var secretOfTheUniverse = [40, 41, 42, 43][2];
		console.log(secretOfTheUniverse);
	}

	// add inner whitespace when you find useful
	if ( !(hungry || sleepy) ) {
		console.log('Coding time, yo!');
	}
	```

- `if` statements without curly braces are allowed. This is recommended for short conditional `throw`/`return` statements. In this case, the conditional statement should start in the same line as the `if` statement's closing parethensis.<br>
Multi-line conditions are allowed, in this case logical operators (`&&`, `||`, `?`, `:`) should appear in the beginning of the line (in order to reduce commit noise when the condition is extended).<br>
All of these are valid:

	```js
	if (!window.jQuery) throw 'Can I haz jQueries?';

	if (!window.jQuery) {
		throw 'Can I haz jQueries?';
	}

	if (
		!window.jQuery
		&& !window.Zepto
		&& !window.AnotherjQueryAlternative
		// can add another && line here and only a +1 line commit diff will be generated!
	) throw 'Can I haz jQueries?';
	```

- Prefer single quotes (apostrophes) as string delimiters. Double quotes may be used when the string contains single quotes.

	```js
	console.log(
		'Sup, double-quoted string?',
		"Hello, I'm a string containing single quotes"
	);
	```

- Lines should be no more than 120 columns long and must be no more than 180 columns long (counting each indentation tab as 4 columns and other characters as a single column each). There are some exceptions to this rule, mainly embedded resources such as Data URIs/base64-encoded content, test fixtures, etc. Note that embedding such resources inside source files are discouraged, prefer to use separate files instead. This provides better modularity and smaller source files which are easier to maintain and lighter on version control systems.

- End of line comments are okay, provided they refer to the single line of code which they are in and fit in the aforementioned line width. All of these are valid:

	```js
	if (zombiesAreComing) return; // run for your lives

	// run for your lives
	if (zombiesAreComing) return;

	/* Run for your lives
	 * blah, yada
	 * yada yada bleh.
	 * Lorem ipsum is too mainstream.
	 */
	if (zombiesAreComing) return;
	```

- Checking if an identifier or object property was given a value: use implicit boolean conversion whenever possible. Use `== null` when a [falsy value](http://es5.github.io/#x9.2) is acceptable, this assumes `null` and `undefined` the same as "no value given". Only use `typeof identifier === 'undefined'` as a last resort, due to its overly verbose form.

	```js
	// -- simple feature checking --
	if (!window.webSockets) throw 'Browser does not support webSockets';


	// -- function argument which does not accept falsy values --
	function showString(nonEmptyString) {
		optionalNonEmptyString = optionalNonEmptyString || 'default value';
		console.log(number);
	}

	showString();          // 'default value'
	showString(undefined); // 'default value'
	showString(null);      // 'default value'
	showString('hello');   // 'hello'

	// nonEmptyString is theoretically documented as a non-empty string argument,
	// hence this falls under undefined behavior:
	showString('');        // 'hello'


	// -- function argument which does accept falsy values --
	function showNumber(number) {
		// can't use `number = number || 42` because `0` is an acceptable value.
		if (number == null) number = 42;
		console.log(number);
	}

	showNumber();          // 42 (default)
	showNumber(undefined); // 42 (default)
	showNumber(null);      // 42 (default)
	showNumber(0);         // 0
	showNumber(13);        // 13


	/* There's no example with `typeof` because I'm yet to see an use case where
	 * `typeof` can't be easily replaced by one of the alternatives above.
	 */
	```

- Use `hasOwnProperty` for objects which may contain arbitrary property names.

	```js
	var stored = {};
	function store(key, value) {
		stored[key] = value;
	}
	function get(key) {
		return stored.hasOwnProperty(key) ? stored[key] : null;
	}
	store('ownProp', 42);

	get('ownProp');  // 42
	get('toString'); // null
	```

- Equality comparison: must use strict equality comparison (`===` and `!==`). The only notable exception to this rule is `== null`, which can be used to check whether a value is `null`/`undefined`.

## Conclusion

All code should look like a single **conscious** person wrote it. A person who can take good style decisions depending on context, be it adding inner whitespace to some parentheses or deciding the placement of a comment.

This style guide will always be a work in progress, in order to keep up with the consistency needs of the projects which adopt it. Although the focus of this style guide is to be the definitive guide for Ult Foundation's projects, this guide is distributed under MIT licensing, hence feel free to adopt it in your own projects as well.

Got any doubts? Found some part of the guide unclear? Want to discuss the decisions' reasoning or add to this guide? Just [open an issue](https://github.com/UltFoundation/UltStyle.js/issues/new). `:)`
