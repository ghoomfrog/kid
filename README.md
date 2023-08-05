![kid_horizontal](https://github.com/ghoomy/kid/assets/35694451/05ceea19-d803-49af-97ab-b788f4173266)

# Hey, kids!

Kid is a minimalist, sandboxed, dynamically typed scripting language.

I'm working on a cross-platform interpreter. Enjoy the design in the meantime. :)

# Let's begin!

## Comments

Kid only has inline comments, prefixed with `\`.

```kid
\ Hey, kids (again)!
```

## Nulls

Nulls are the no-values: they denote that there is no value.

```kid
...
```

They're written as an ellipsis because they can be used as empty expressions (like `pass` in python) or placeholders.

## Integers

Integers in Kid are 64-bit and are dynamically signed.

```kid
-123
```

`+123` is not what you think it is in Kid. The prefix operator `+` is used for something completely different. It's redundant to put `+` in front of an already positive numeral anyway, so I used that chance to use it for a more useful feature. You'll learn about it later in this guide.

## Floating-Point Numbers

Floating-point numbers, or floats, in Kid are 64-bit.

```kid
4.56
```

`4.0` and `0.56` can't be shortened to `4.` and `.56`. This is to reduce confusion with adjacent syntax that uses `.` like `...`.

## Strings

```kid
":)"
```

Single quotation marks are not allowed: ~~`':('`~~. It's so relieving to not have to worry about two quotation mark types, isn't it? I chose `"` because apostrophes are more common than double quotes in English: contractions are more common than quoting.

Kid supports syntactic sugar for letter strings: ones containing letters in any Unicode-supported language.

```
ghoom
```

This is equivalent to `"ghoom"`.

Underscores (`_`) are excluded for simplicity, and digits are excluded to improve readability and distinguishability from numerals.

### Escape Sequences

* `\\` — `\`
* `\'` — `'`
* `\"` — `"`
* `\u(N)` — the unicode character with the code as the integer numeral `N`
* `\0` — NUL
* `\t` — TAB
* `\n` — LF
* `\r` — CR
* `\e` — ESC

## Variables

Variable names can be any value.

```kid
favFood = "chicken nuggets"
```

In this case, the name is the string `favFood`.

Assignment expressions return the variable name (`favFood` in this case).

The prefix operator `$` can be used to access the value of a variable.

```kid
$favFood
```

This returns `"chicken nuggets"`.

We can manually delete (garbage-collect) variables by setting them to null.

```kid
favFood = ...
```

Accessing undefined variables returns null.

It's also good practice to explicitely null-define variables that you're only going to use later.

## Lists

Lists can store mixed types of values.

```kid
myList = ":)" 123 4.56
```

Yup! That's a list! Items are only separated with whitespace or themselves.

Notice how this alone makes it impossible to make lists start out with less than two items. To fix this problem, lists can't start out with nulls in them.

```kid
myList = ":)"...
```

Now Kid can detect that this is intended to be a list *and* make it only start out with one item. Genius!

```kid
myList = ... ...
```

And this is how to make a list start out empty.

We can access a list's length by prefixing its name with `%`.

```kid
%myList
```

If this is used on nulls, it returns 0. Otherwise, if it's used on other non-list values, it returns 1.

Now let's move on to indexing.

```kid
myList#0
```

This is a reference to the item at index `0`. References are just values. They can be used in assignments to set the item to a new value.

```kid
myList#0 = ":D"
```

And just like variables, `$` will get the value of the referenced item.

```kid
$myList#0
```

As you can see, `$` has a lower precedence than `#`, making it unnecessary to do `$(myList#0)`.

Kid supports negative indices, and accessing items that are out of bounds returns null.

To append an item, we just set the item whose index is the list's length.

```kid
myList#%myList = newItem
```

To delete the last item, we just set it to null, which will decrement the list's length.

```kid
myList#-1 = ...
```

All these operations can be done on strings too, with items being Unicode characters.

If indexing syntax is used with non-list values, the index `0` refers to the value, and other indices refer to null.

```kid
n = 1
n#0 \ returns $n
n#1 \ returns ...
```

### Separating Statements

Statements are just list items. One neat feature is that newlines have a lower precedence than most operators. You'll learn what has lower precedence than newlines later in this guide.

```kid
favFood = "chicken nuggets"
favPetAnimal = "cat"
```

If this wasn't a feature, the latter would be interpreted as:

```kid
favFood = "chicken nuggets" (favPetAnimal = "cat")
```

## Blobs

Blobs are global variables stored in disk (alongside memory). They have the same assignment syntax except `=` is replaced by `:`.

```kid
todos:
	"do stuff"
	"do more stuff"
	"pet self"
	"do even more stuff damn it!"

...

todos#3 = "ok i think i'm good for today"
```

The next time the program runs, `todos` will be automatically in whatever state it was on before the program ended.

Deleting a blob from disk is done by setting it to null.

```kid
todos: ...
```

Blobs have length limits because of filename limitations in modern filesystems.

## Miscellaneous Operators

Here's some miscellaneous operators:

### Numeric Operators

- `-$x`
- `$x + $y`
- `$x - $y`
- `$x * $y`
- `$x / $y`
- `$x % $y` (division remainder)
- `~$x` (bitwise inversion)
- `$x | $y` (bitwise OR)
- `$x ^ $y` (bitwise XOR)
- `$x & $y` (bitwise AND)
- `$x << $y` (left arithmetic shift)
- `$x <<< $y` (left logical shift)
- `$x >> $y` (right arithmetic shift)
- `$x >>> $y` (right logical shift)

Nulls are treated as 0 in numeric operations.

### Relational Operators

These operators return the right hand operand on success, and null otherwise.

- `$x == $y`
- `$x < $y`

Nulls are treated as 0 in `<`.

These operators have lower precedences than the numeric ones above.

## Default Values

We can use the prefix operator `^` to get the default value of a value's type.

```kid
^...         \ ...
^123         \ 0
^4.56        \ 0.0
^":)"        \ ""
^":)"...     \ ... ...
^{$? + 1}    \ {}
^@2130706433 \ @0
^@(0 1)      \ @(0 0)
```

One benefit to this is checking a value's type by comparing its default value.

You'll learn more about the other types later in this guide, don't worry. ;)

## Conditionals

Kid features two types of inline conditionals. Both of them consider nulls to be the only falsy value.

The first is the if-then conditional:

```kid
$female -> ">:o"
```

If `$female` is truthy (not null), this returns `">:o"`. Otherwise, it returns `$female`.

The second type is the if-not-then conditional:

```kid
$female |> ":|"
```

If `$female` is null, this returns `":|"`. Otherwise, it returns `$female`.

`|>` can be used to specify a fallback in case a value is null.

`->` and `|>` can be combined to make an if-else combination:

```kid
$female -> ">:o" |> ":|"
```

If `$female` is truthy, this returns `">:o"`. Otherwise, it returns `":|"`.

These operators have lower precedences than newlines.

Operands of `->` and `|>` are optional, defaulting to null. 

## Groups

Groups are expressions that have higher precedence than their environment. They're enclosed in `()`.

```kid
1 + 1 * 2
```

This returns 3 because `*` has a higher precedence than `+`. If we want to prioritize `+`, we do this:

```kid
(1 + 1) * 2
```

Now this returns 4.

Of course, groups can be nested.

## Blocks

Blocks are scoped groups of statements treated as one expression.

```kid
$female ->
	face = ">:o"
	interest = 1.0
```

Lines with the same tab indentation belong to same block.

By default, blocks return the last last expression. We can use the prefix operator `=` to override that.

```kid
preparedness = $female ->
	face = ">:o"
	= 0.0
	interest = 1.0
```

Here, the block returns `0.0`, and if `$female` is truthy, the conditional returns `0.0` which is then assigned to `preparedness`.

Now the `interest` assignment is ignored. Also, the prefix operator `=` can be used without an operand in order to immediately return null.

## Loops

Kid only features Until loops at its core, using the binary operator `=>`.

```kid
nLooks = 0
$male =>
	nLooks = nLooks + 1
```

Until `$male` is truthy (while it's null), this increments `nLooks` by `1`.

Operands of `=>` are optional, defaulting to null. 

## Functions

All functions in Kid are scoped coroutines with one optional parameter and one return value which is null by default.

```kid
plusOne = {$? + 1}
```

Here, `plusOne` is assigned a function that returns `$? + 1`.

`?` is a reference to the argument. It defaults to null if no argument was passed.

Calling a function is done using the prefix operator `/` with the function's name, followed by an optional argument.

```kid
/plusOne 99
```

The prefix operator `/` has a lower precedence than lists, so we can do this:

```kid
/duplicate ditto 20
```

`ditto 20` is a list passed as an argument to `duplicate`. Notice how I didn't need to do `/duplicate(ditto 20)`.

The operator also has lower precedence than other prefix `/` operators toward its right.

Arguments shares scopes with the body of the function regardless if they're blocks or not.

```kid
/for i=1 10 {/shout $?}
```

Here, `i` is defined inside the function `for`.

To return from a function without resetting its state: without making it start from the top the next time it's called, we use the prefix operator `>`. This is *the* coroutine feature.

Using the prefix operator `/` on variable names of non-function values returns them.

## Time

`%%` is millisecond-level Unix timestamp integer. In addition to keeping the time with it, we can use it to wait a number of milliseconds.

```kid
wait = {
	start    = %%
	duration = $?
	end      = $start + $duration
	
	$duration < $end - $start |> $duration == $end - $start => ...
}
```

## Asynchronous Evaluation

Expressions can be evaluated asynchronously if they're enclosed in `[]`.

```kid
a = 1
[
	/wait 1000
	c = 3
]
b = 2
```

Here, `a` and `b` are defined before `c` because `c` was defined asynchronously after `1000` milliseconds.

## Sockets

```kid
@2130706433
@(0 1)
```

Both of these are IP-level sockets connected to the local host.

`2130706433` is the raw integer that is the IPv4 address 127.0.0.1.

Since integers can be up to 64 bits, and IPv6 addresses are 128-bit, a list of two integers is used to represent IPv6 addresses after `@`. `(0 1)` is equivalent to the IPv6 address 0000:0000:0000:0000:0000:0000:0000:0001. The first item `0` corresponds to the first half of the address (0000:0000:0000:0000), and thhe second item corresponds to the second half (0000:0000:0000:0001).

We can send lists of integers as data through sockets using the binary operator `<~`:

```kid
@2130706433 <~ 21 43 65
```

We can also assign a function as the receiver of data coming from a socket using the binary operator `~>`:

```kid
@2130706433 ~> {/doSomethingWith $?}
```

The function is called synchronously with a string argument.

A special case of sockets is `@0` and `@(0 0)` which don't do anything with data.

## Modules

All modules are global and are stored in one folder. The prefix operator `+` is how we include modules by their names.

```kid
+ discord
```

Module names are not the same as their filenames. Just like variable names, modules names can be of any type (`discord` here is of course just a string). But just like blobs, they also have length limits.

Kid ensures that the same module is never included more than once.
