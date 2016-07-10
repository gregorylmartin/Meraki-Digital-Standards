#PHP Standards

Some parts of the Meraki Digital code structure for PHP markup are inconsistent in their style. Meraki Digital is working to gradually improve this by helping users maintain a consistent style so the code can become clean and easy to read at a glance.

Keep the following points in mind when writing PHP code for Meraki Digital, whether for core programming code, plugins, or other utilities. The guidelines are similar to [Pear](https://pear.php.net/manual/en/standards.php) standards in many ways, but differ in some key respects.

#PHP

##Single and Double Quotes

Use single and double quotes when appropriate. If you're not evaluating anything in the string, use single quotes. You should almost never have to escape quotes in a string, because you can just alternate your quoting style, like so:

```php
echo '<a href="/static/link" title="Yeah yeah!">Link name</a>';
echo "<a href='$link' title='$linktitle'>$linkname</a>";
```
Text that goes into attributes should be run through esc_attr() so that single or double quotes do not end the attribute value and invalidate the HTML and cause a security issue. 


##Indentation

Your indentation should always reflect logical structure. Use real tabs and not spaces, as this allows the most flexibility across clients.

Exception: if you have a block of code that would be more readable if things are aligned, use spaces:

```php
[tab]$foo   = 'somevalue';
[tab]$foo2  = 'somevalue2';
[tab]$foo34 = 'somevalue3';
[tab]$foo5  = 'somevalue4';
```
For associative arrays, values should start on a new line. Note the comma after the last array item: this is recommended because it makes it easier to change the order of the array, and makes for cleaner diffs when new items are added.

```php
$my_array = array(
[tab]'foo'   => 'somevalue',
[tab]'foo2'  => 'somevalue2',
[tab]'foo3'  => 'somevalue3',
[tab]'foo34' => 'somevalue3',
);
```
Rule of thumb: Tabs should be used at the beginning of the line for indentation, while spaces can be used mid-line for alignment.


##Brace Style

Braces shall be used for all blocks in the style shown here:

```php
if ( condition ) {
    action1();
    action2();
} elseif ( condition2 && condition3 ) {
    action3();
    action4();
} else {
    defaultaction();
}
```
Furthermore, if you have a really long block, consider whether it can be broken into two or more shorter blocks or functions. If you consider such a long block unavoidable, please put a short comment at the end so people can tell at glance what that ending brace ends – typically this is appropriate for a logic block, longer than about 35 rows, but any code that's not intuitively obvious can be commented.

Braces should always be used, even when they are not required:

```php
if ( condition ) {
    action0();
}

if ( condition ) {
    action1();
} elseif ( condition2 ) {
    action2a();
    action2b();
}

foreach ( $items as $item ) {
    process_item( $item );
}
```
Note that requiring the use of braces just means that single-statement inline control structures are prohibited. You are free to use the alternative syntax for control structures (e.g. if/endif, while/endwhile)—especially in your templates where PHP code is embedded within HTML, for instance:

```php
<?php if ( have_posts() ) : ?>
    <div class="hfeed">
        <?php while ( have_posts() ) : the_post(); ?>
            <article id="post-<?php the_ID() ?>" class="<?php post_class() ?>">
                <!-- ... -->
            </article>
        <?php endwhile; ?>
    </div>
<?php endif; ?>
```
##Use elseif, not else if

else if is not compatible with the colon syntax for if|elseif blocks. For this reason, use elseif for conditionals.


##Regular Expressions

Perl compatible regular expressions (PCRE, preg_ functions) should be used in preference to their POSIX counterparts. Never use the /e switch, use preg_replace_callback instead.

It's most convenient to use single-quoted strings for regular expressions since, contrary to double-quoted strings, they have only two metasequences: \' and \\.


##No Shorthand PHP Tags

Important: Never use shorthand PHP start tags. Always use full PHP tags.

Correct:

```php
<?php ... ?>
<?php echo $var; ?>
```

Incorrect:

```php
<? ... ?>
<?= $var ?>
```

##Remove Trailing Spaces

Remove trailing whitespace at the end of each line of code. Omitting the closing PHP tag at the end of a file is preferred. If you use the tag, make sure you remove trailing whitespace.


##Space Usage

Always put spaces after commas, and on both sides of logical, comparison, string and assignment operators.

```php
x == 23
foo && bar
! foo
array( 1, 2, 3 )
$baz . '-5'
$term .= 'X'
```

Put spaces on both sides of the opening and closing parenthesis of if, elseif, foreach, for, and switch blocks.

```php
foreach ( $foo as $bar ) { ...
```

When defining a function, do it like so:

```php
function my_function( $param1 = 'foo', $param2 = 'bar' ) { ...
```

When calling a function, do it like so:

```php
my_function( $param1, func_param( $param2 ) );
```

When performing logical comparisons, do it like so:

```php
if ( ! $foo ) { ...
```

When type casting, do it like so:

```php
foreach ( (array) $foo as $bar ) { ...

$foo = (boolean) $bar;
```

When referring to array items, only include a space around the index if it is a variable, for example:

```php
$x = $foo['bar']; // correct
$x = $foo[ 'bar' ]; // incorrect

$x = $foo[0]; // correct
$x = $foo[ 0 ]; // incorrect

$x = $foo[ $bar ]; // correct
$x = $foo[$bar]; // incorrect
```

##Formatting SQL statements

When formatting SQL statements you may break it into several lines and indent if it is sufficiently complex to warrant it. Most statements work well as one line though. Always capitalize the SQL parts of the statement like UPDATE or WHERE.

Functions that update the database should expect their parameters to lack SQL slash escaping when passed. Escaping should be done as close to the time of the query as possible.


##Database Queries

Avoid direct database queries where possible and practical. If there is a defined stored procedure or function that can get the data you need, use it. Database abstraction (using functions/procedures instead of queries) helps keep your code forward-compatible and, in cases where results are cached in memory, it can be many times faster.


##Naming Conventions

Use lowercase letters in variable, action, and function names (<b>never camelCase</b>). Separate words via underscores. Don't abbreviate variable names un-necessarily; let the code be unambiguous and self-documenting.  The notable exception to ambiguity is in looping structures and in common data packages that will be passed to other views or functions.

(Need GLM expansion here)

```php
function some_name( $some_variable ) { [...] }
```

Class names should use capitalized words separated by underscores. Any acronyms should be all upper case.

```php
class Walker_Category extends Walker { [...] }
class MD_HTTP { [...] }
```

Constants should be in all upper-case with underscores separating words:

```php
define( 'DOING_AJAX', true );
```

Files should be named descriptively using lowercase letters. Hyphens should separate words.

```php
my-plugin-name.php
```

Class file names should be based on the class name with class- prepended and the underscores in the class name replaced with hyphens, for example MD_Error becomes:

```php
class-md-error.php
```

```php
general-template.php
```

##Self-Explanatory Flag Values for Function Arguments

Prefer string values to just true and false when calling functions.

```php
// Incorrect
function eat( $what, $slowly = true ) {
...
}
eat( 'mushrooms' );
eat( 'mushrooms', true ); // what does true mean?
eat( 'dogfood', false ); // what does false mean? The opposite of true?
```

Since PHP doesn't support named arguments, the values of the flags are meaningless, and each time we come across a function call like the examples above, we have to search for the function definition. The code can be made more readable by using descriptive string values, instead of booleans.

```php
// Correct
function eat( $what, $speed = 'slowly' ) {
...
}
eat( 'mushrooms' );
eat( 'mushrooms', 'slowly' );
eat( 'dogfood', 'quickly' );
```

When more words are needed to describe the function parameters, an $args array may be a better pattern.

```php
// Even Better
function eat( $what, $args ) {
...
}
eat ( 'noodles', array( 'speed' => 'moderate' ) );
```

##Ternary Operator

Ternary operators are fine, but always have them test if the statement is true, not false. Otherwise, it just gets confusing. (An exception would be using ! empty(), as testing for false here is generally more intuitive.)

For example:

```php
// (if statement is true) ? (do this) : (else, do this);
$musictype = ( 'jazz' == $music ) ? 'cool' : 'blah';
// (if field is not empty ) ? (do this) : (else, do this);
```

##Yoda Conditions

```php
if ( true == $the_force ) {
    $victorious = you_will( $be );
}
```

When doing logical comparisons, always put the variable on the right side, constants or literals on the left.

<b>Here's Why:</b> In the above example, if you omit an equals sign (admit it, it happens even to the most seasoned of us), you'll get a parse error, because you can't assign to a constant like true. If the statement were the other way around... 
```php
if ($the_force = true ) {
```
the assignment would be perfectly valid, returning 1, causing the if statement to evaluate to true, and you could be chasing that bug for a while.

> A little bizarre, it is, to read. Get used to it, you will. - Yoda, Jedi Master

This applies to ==, !=, ===, and !==. Yoda conditions for <, >, <= or >= are significantly more difficult to read and are best avoided.


##Clever Code

In general, readability is more important than cleverness or brevity.

```php
isset( $var ) || $var = some_function();
```

Although the above line is clever, it takes a while to grok if you're not familiar with it. So, just write it like this:

```php
if ( ! isset( $var ) ) {
    $var = some_function();
}
```

##Error Control Operator @

As noted in the PHP docs:

PHP supports one error control operator: the at sign (@). When prepended to an expression in PHP, any error messages that might be generated by that expression will be ignored.

While this operator does exist in Core, it is often used lazily instead of doing proper error checking. Its use is highly discouraged, as even the PHP docs also state:

Warning: Currently the “@” error-control operator prefix will even disable error reporting for critical errors that will terminate script execution. Among other things, this means that if you use “@” to suppress errors from a certain function and either it isn't available or has been mistyped, the script will die right there with no indication as to why.


##Don't extract()

extract() is a terrible function that makes code harder to debug and harder to understand. We should discourage it's use and remove all of our uses of it.


##Credits

PHP standards: [Pear Standards](https://pear.php.net/manual/en/standards.php)
