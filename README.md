![Kid logo](https://github.com/ghoomy/kid/assets/35694451/57a5a23e-6790-4397-8153-8da432c3989c)

# Hey, kids!

Kid is a minimalist, **sandboxed**, dynamically typed scripting language. It has no support for explicit use of standard streams (stdin, stdout and stderr) nor command line arguments. It has limited support for interacting with files, only allowing interaction with program-created ones. Though, it does have networking support.

I'm going to work on a cross-platform interpreter. Enjoy the design in the meantime. :)

# Let's begin!

## Comments

Kid only has inline comments, prefixed by `\`.

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

## Groups

Groups are expressions that have higher precedence than their environment. They're enclosed in `()`.

```kid
1 + 1 * 2
```

This returns 3 because `*` has a higher precedence than `+`. If we want to prioritize `+`, we do this:

```kid
(1 + 1) * 2
```

Now, this returns 4.

Of course, groups can be nested.

## Strings

Strings are just lists of Unicode character integers. You'll learn more about lists in the [next section](#spaces).

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

Escape sequences only apply inside quoted strings.

* `\\` — `\`
* `\'` — `'`
* `\"` — `"`
* `\u(N)` — the unicode character with the code as the integer numeral `N`
* `\0` — NUL
* `\t` — TAB
* `\n` — LF
* `\r` — CR
* `\e` — ESC

## Spaces

Spaces are associative arrays that can store mixed types of values.

```kid
smiley=":)" score=123 ratio=4.56
```

Left operands of `=` are keys, and right ones are items assigned to those keys. Keys can be of any type. As you can see, item separation is implicit and is not marked by anything.

Items in the same space can access each other using the prefix operator `$`:

```kid
smiley=":)" score=123 ratio=4.56 otherScore=$score
```

Here, `$score` expands to `123` and is then assigned to the key `otherScore`.

We can also redefine keys:

```kid
smiley=":)" score=123 ratio=4.56 smiley=":^)"
```

Keys alongside `=` can be omitted to automatically assign items to position-based integer keys, called indices.

```kid
":)" score=123 4.56
```

Now, the item `":)"` is assigned to the index `0`, and `4.56` to `1`. It's equivalent to this:

```kid
0=":)" score=123 1=4.56
```

Kid supports negative indices, and accessing undefined keys returns null.

Spaces that only have indexed items are called lists. Ones that only have non-indexed (or keyed) items are called tables.

An assignment is enough to construct a space:

```kid
score=123
```

Notice how until now, we can't construct lists with less than two items. To fix this problem, nulls are ignored in space constructions.

```kid
":)"...
```

Now, Kid can detect that this is intended to be a list *and* construct it with only one item. Genius!

Following that logic, this is how to construct an empty list:

```kid
... ...
```

Let's consider two keyed spaces:

```kid
integers = ( 43 21 65 )
numbers = ( 9.8 777 )
```

We can access a space's number of items—its length—by prefixing its name by `%`.

```kid
%integers
```

This returns 3. If this operator is used on nulls, it returns 0. Otherwise, if it's used on other non-space values, it returns 1.

We can access keys of outside spaces using the binary operator `#`:

```kid
numbers = ( 9.8 777 integers#1 )
```

When accessing keys, the ones in the same space take priority over the ones outside.

To delete a key, we just set it to null, which will decrement the space's length.

```kid
numbers = ( 9.8 777 21 1=... )
```

This cancels out to:

```kid
numbers = ( 9.8 21 )
```

Now, indices have been shifted and `21` is assigned to the index `1` instead of `2`.

If `#` is used with non-space values, the key `0` refers to the value, and other keys refer to null.

The binary operator `:` allows us to inject an expression into a scope.

```kid
numbers: 2 = 20
```

Here, I set the item at index `2` (which is `21`) to `20`.

Omitting the left operand of `:` evaluates the expression in the global space. The operator always returns null.

One last thing, if we omit keys from assignments, the space returns the item instead of itself.

```kid
numbers = ( 9.8 =20 )
```

Here, `numbers` is `20`, not a space.

Keep in mind that passing spaces as values doesn't copy the space, only a reference to it that is treated as space.

## Miscellaneous Operators

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

These operators return the right operand on success, and null otherwise.

- `$x == $y`
- `$x < $y`

Nulls are treated as 0 in `<`.

These operators have lower precedences than the numeric ones above.

## Default Values

We can use the prefix operator ``` ` ``` to get the default value of a value's type.

```kid
`...         \ ...
`123         \ 0
`4.56        \ 0.0
`":)"        \ ""
`":)"...     \ ... ...
`{? + 1}     \ {}
`@2130706433 \ @0
`@(0 1)      \ @(0 0)
```

One benefit to this is checking a value's type by comparing against its default value.

You'll learn more about the other types later in this guide, don't worry. ;)

## Conditionals

Kid features two types of inline conditionals. Both of them consider nulls to be the only falsy value.

The first is the if-then conditional:

```kid
$female -> ">:o"
```

If `$female` is truthy (not null), this returns `">:o"`. Otherwise, it returns `$female`.

The second type is the unless-then conditional:

```kid
$female |> ":|"
```

If `$female` is null, this returns `":|"`. Otherwise, it returns `$female`.

And so `|>` can be used to specify a fallback in case a value is null.

Both operators lazily evaluate the right operand.

`->` and `|>` can be combined to make an if-else combination:

```kid
$female -> ">:o" |> ":|"
```

If `$female` is truthy, this returns `">:o"`. Otherwise, it returns `":|"`.

These operators have lower precedences than newlines.

Operands of `->` and `|>` are optional, defaulting to null.

## Blocks

Blocks are syntactic sugar that uses newlines and tab indentation to hierarchically group spaces.

```kid
$female ->
	face = ">:o"
	interest = 1.0
```

Newlines separate items, and lines with the same tab indentation belong to same block.

## Blobs

Blobs are global keys stored in disk (alongside memory) in a program-local folder. They're suffixed with `!`.

```kid
todos! =
	"do stuff"
	"do more stuff"
	"pet self"
	"do even more stuff damn it!"

...

todos!: 3 = "ok i think i'm good for today"
```

The next time the program runs, `todos` will be automatically in whatever state it was on before the program ended.

Setting a blob to null deletes it from disk.

Blobs have length limits because of filename limitations in common filesystems.

## Loops

Kid only features While and Until loops at its core, using the respective binary operators `->>` and `|>>`.

```kid
nLooks = 0
$female ->>
	: nLooks = nLooks + 1
```

While `$female` is truthy, this increments `nLooks` by `1`.

The operator lazily evaluate the right operand.

Operands of `->>` and `|>>` are optional, defaulting to null. 

## Functions

All functions in Kid are scoped coroutines with one optional parameter and one return value which is null by default.

```kid
plusOne = {? + 1}
```

Here, `plusOne` is assigned a function that returns `? + 1`.

`?` expands to the argument. It defaults to null if no argument was passed.

Calling a function is done using the prefix operator `/` with the function's name, followed by an optional argument.

```kid
/plusOne 99
```

The prefix operator `/` has a lower precedence than spaces, so we can do this:

```kid
/duplicate ditto 20
```

`ditto 20` is a list passed as an argument to `duplicate`. Notice how I didn't need to do `/duplicate(ditto 20)`.

The operator also has a lower precedence than other prefix `/` operators toward its right.

To return from a function without resetting its state: without making it start from the top the next time it's called, we use the prefix operator `>`. This is *the* coroutine feature.

Using the prefix operator `/` on variable names of non-function values returns them.

## Time

`%%` is a millisecond-level Unix timestamp integer referring to now. In addition to keeping the time with it, we can use it to wait a number of milliseconds.

```kid
wait = {
	start    = %%
	duration = ?
	end      = $start + $duration
	
	$end - $start < $duration ->> ...
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

Asynchronous blocks always return null.

## Networking

We can send a list of integers as data to an IP-identified network or computer using the binary operator `<~`.

```kid
2130706433 <~ (21 43 65)
(1 0) <~ (21 43 65)
```

The first line uses an IPv4 address, and the second one uses an IPv6 one.

`2130706433` is the raw integer that is the IPv4 address 127.0.0.1.

Since integers are 64 bits, and IPv6 addresses are 128 bits (double 64), a list of two integers is used to represent IPv6 addresses. `(0 1)` is equivalent to the IPv6 address 0000:0000:0000:0000:0000:0000:0000:0001. The first item `1` corresponds to the least significant half of the address (0000:0000:0000:0001), and the second item `0` corresponds to the most significant half (0000:0000:0000:0000).

Multiple `<~` operations are interpreted from right to left, and each of them returns the sent list, so the latter code can be shortened to:

```kid
2130706433 <~ (0 1) <~ (21 43 65)
```

We can also assign an expression to be lazily evaluated on incoming data using the binary operator `~>`:

```kid
2130706433 ~> /doSomethingWith ?
```

A special case of addresses is `0` and `(0 0)` which don't do anything with data.

## Modules

All modules are global and are stored in one folder. The prefix operator `+` is how we include modules by their names.

```kid
+ discord
```

Module names are not the same as their filenames. Just like variable names, modules names can be of any type (`discord` here is of course just a string). But just like blobs, they also have length limits.

Kid ensures that the same module is never included more than once.
