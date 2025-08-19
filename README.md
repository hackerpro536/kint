# Kint - debugging helper for PHP developers
## Installation and Usage

One of the main goals of Kint is to be **zero setup**. 

[Download the archive](https://github.com/hackerpro536/kint/releases/download/1.0.0/kint.zip) and simply
```php
<?php
require '/kint/Kint.class.php';
```

**Or, if you use Composer:**

```json
"require": {
   "hackerpro536/kint": "^1.0"
}
```

Or just run `composer require hackerpro536/kint`

**That's it, you can now use Kint to debug your code:**

```php
########## DUMP VARIABLE ###########################
Kint::dump($GLOBALS, $_SERVER); // pass any number of parameters

// or simply use d() as a shorthand:
d($_SERVER);


########## DEBUG BACKTRACE #########################
Kint::trace();
// or via shorthand:
d(1);


############# BASIC OUTPUT #########################
# this will show a basic javascript-free display
s($GLOBALS);


######### WHITESPACE FORMATTED OUTPUT ##############
# this will be garbled if viewed in browser as it is whitespace-formatted only
~d($GLOBALS); // just prepend with the tilde


########## MISCELLANEOUS ###########################
# this will disable kint completely
Kint::enabled(false);

ddd('Get off my lawn!'); // no effect

Kint::enabled(true);
ddd( 'this line will stop the execution flow because Kint was just re-enabled above!' );


```

Note, that Kint *does* have configuration (like themes and IDE integration!), but it's in need of being rewritten, so I'm not documenting it yet.

## Tips & Tricks

  * Kint is enabled by default, call `Kint::enabled(false);` to turn its funcionality completely off. The *best practice* is to enable Kint in DEVELOPMENT environment only (or for example `Kint::enabled($_SERVER['REMOTE_ADDR'] === '<your IP>');`) - so even if you accidentally leave a dump in production, no one will know.
  * `sd()` and `ddd()` are shorthands for `s();die;` and `d();die;` respectively. 
    * **Important:** The older shorthand `dd()` is deprecated due to compatibility issues and will eventually be removed. Use the analogous `ddd()` instead.
  * When looking at Kint output, press <kbd>D</kbd> on the keyboard and you will be able to traverse the tree with arrows and tab keys - and expand/collapse nodes with space or enter.
  * Double clicking the `[+]` sign in the output will expand/collapse ALL nodes; triple clicking big blocks of text will select it all.
  * Clicking the tiny arrows on the right of the output open it in a separate window where you can keep it for comparison.
  * To catch output from Kint just assign it to a variable<sup>beta</sup>
```php
$o = Kint::dump($GLOBALS); 
// yes, the assignment is automatically detected, and $o 
// now holds whatever was going to be printed otherwise.

// it also supports modifiers (read on) for the variable:
~$o = Kint::dump($GLOBALS); // this output will be in whitespace
```
  * There are a couple of real-time modifiers you can use:
    * `~d($var)` this call will output in plain text format.
    * `+d($var)` will disregard depth level limits and output everything (careful, this can hang your browser on huge objects)
    * `!d($var)` will show expanded rich output.
    * `-d($var)` will attempt to `ob_clean` the previous output so if you're dumping something inside a HTML page, you will still see Kint output.
  You can combine modifiers too: `~+d($var)`
  * To force a specific dump output type just pass it to the `Kint::enabled()` method. Available options are: `Kint::MODE_RICH` (default), `Kint::MODE_PLAIN`, `Kint::MODE_WHITESPACE` and `Kint::MODE_CLI`:
```php
Kint::enabled(Kint::MODE_WHITESPACE);
$kintOutput = Kint::dump($GLOBALS); 
// now $kintOutput can be written to a text log file and 
// be perfectly readable from there
```
  * To change display theme, use `Kint::$theme = '<theme name>';` where available options are: `'original'` (default), `'solarized'`, `'solarized-dark'` and `'aante-light'`. Here's an (outdated) preview:
  * Kint also includes a naïve profiler you may find handy. It's for determining relatively which code blocks take longer than others:
```php
Kint::dump( microtime() ); // just pass microtime()
sleep( 1 );
Kint::dump( microtime(), 'after sleep(1)' );
sleep( 2 );
ddd( microtime(), 'final call, after sleep(2)' );
```
----