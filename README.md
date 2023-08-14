![Kid logo](https://github.com/ghoomy/kid/assets/35694451/5b3749a3-051e-41c2-9359-3c8c80733359)

# Hey, kids!

Kid is a minimalist, sandboxed, dynamically typed scripting language. It has no support for explicit use of standard streams (stdin, stdout and stderr) nor command line arguments. It has limited support for interacting with files, only allowing interaction with program-local, program-created ones. Though, it does have networking support.

I'm going to work on a cross-platform interpreter and the standard library (someday). Enjoy the design in the meantime. :)

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

## Quantities

Quantities in Kid are unsigned integers that are dynamically sized (with an 1-byte minimum).

```kid
123
```

Quantity literals can contain a single `.` as a readability enhancement.

```kid
100.00
```

This is equivalent to `10000`.

## Strings

Strings are just lists of Unicode character quantities. You'll learn more about lists in the [next section](#spaces). Strings are enclosed in `"`.

```kid
":)"
```

Kid supports syntactic sugar for letter strings: ones containing ASCII letters.

```
ghoom
```

This is equivalent to `"ghoom"`.

Letter strings are case-sensitive.

### Escape Sequences

Escape sequences only apply inside quoted strings.

- `\\` — `\`
- `\'` — `'`
- `\"` — `"`
- `\(N)` — the unicode character with the code as the (decimal) integer numeral `N`
- `\0` — NUL
- `\t` — TAB
- `\n` — LF
- `\r` — CR
- `\e` — ESC

## Spaces

Spaces are associative arrays that can store mixed types of keys and items.

```kid
smiley=":)" score=123 percentage=45.00
```

Left operands of `=` are keys, and right ones are items assigned to those keys. Keys can be of any type. As you can see, item separation is implicit and is not marked by anything.

Items in the same space can access each other using the prefix operator `$`:

```kid
smiley=":)" score=123 percentage=45.00 otherScore=$score
```

Here, `$score` expands to `123` and is then assigned to the key `otherScore`.

We can also redefine keys:

```kid
smiley=":)" score=123 percentage=45.00 smiley=":^)"
```

Keys alongside `=` can be omitted to automatically assign items to position-based quantity keys, called indices.

```kid
":)" score=123 45.00
```

Now, the item `":)"` is assigned to the index `0`, and `45.00` to `1`. It's equivalent to this:

```kid
0=":)" score=123 1=45.00
```

Accessing undefined keys returns null.

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
quantities =
	43
	21
	65
otherQuantities =
	198
	777
```

You'll learn more about using tabs [later](#blocks).

We can access a space's number of items—its length—by prefixing its name by `%`.

```kid
%quantities
```

This returns 3. If this operator is used on nulls, it returns 0. Otherwise, if it's used on other non-space values, it returns 1.

We can access keys of outside spaces using the binary operator `#`:

```kid
otherQuantities =
	198
	777
	quantities#1
```

When accessing keys, the ones in the same space take priority over the ones outside.

To delete a key, we just set it to null, which will decrement the space's length.

```kid
otherQuantities =
	198
	777
	21
	1=...
```

This cancels out to:

```kid
otherQuantities =
	198
	21
```

Now, indices have been shifted and `21` is assigned to the index `1` instead of `2`.

If `#` is used with non-space values, the key `0` refers to the value, and other keys refer to null.

The binary operator `:` allows us to append an expression at the end of a space's constructor.

```kid
otherQuantities: 20
```

Now, `otherQuantities` is `198 21 20`.

Omitting the left operand of `:` evaluates the expression in the global space. The operator always returns null.

Using `:` with an undefined key automatically assigns an empty space to it.

If we omit keys from assignments, the space returns the item instead of itself.

```kid
otherQuantities =
	198
	=20
```

Here, `otherQuantities` is `20`, not a space.

Spaces are assigned and passed by reference.

## Miscellaneous Operators

All of the following operators have equal precedence.

### Numeric Operators

- `$x + $y`
- `$x - $y`
- `$x * $y`
- `$x / $y` (a space where the quotient is at `0` and the remainder is at `remainder`)
- `~$x` (bitwise inversion)
- `$x | $y`
- `$x ^ $y` (bitwise XOR)
- `$x & $y`
- `$x << $y` (left logical shift)
- `$x >> $y` (right logical shift)

In numeric operations, nulls are treated as 0, and spaces are treated as their first quantity item.

### Relational Operators

These operators return the right operand if it's greater, and null otherwise.

- `$x == $y`
- `$x < $y`

Nulls are treated as 0 in `<`.

## Default Values

We can use the prefix operator ``` ` ``` to get the default value of a value's type.

```kid
`...         \ ...
`123         \ 0
`":)"        \ ""
`":)"...     \ ... ...
`{## + 1}    \ {}
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

The left operands of these operators are optional, defaulting to null.

## Blocks

Blocks are syntactic sugar that uses newlines and tabs to hierarchically group spaces.

```kid
$female ->
	face = ">:o"
	interest = 100
```

Newlines into a deeper indentation introduce a space, and newlines into the same indentation separate items.

## Blobs

Blobs are keys stored in disk in a program-local folder. They're prefixed by `@`.

```kid
@todos =
	"do stuff"
	"do more stuff"
	"pet self"
	"do even more stuff damn it!"

...

@todos: 3 = "ok i think i'm good for today"
```

The next time the program runs, `todos` will be automatically in whatever state it was on before the program ended. And if `todos` was part of a space, that space will get loaded too even if it's not assigned to a blob.

Setting a blob or its containing space to null deletes it from disk.

Blobs have length limits because of filename limitations in common filesystems.

## Loops

Kid only features While and Until loops at its core, using the respective binary operators `->>` and `|>>`.

```kid
nLooks = 0
{$female} ->>
	: nLooks = nLooks + 1
```

The left operand is a function that gets called in every iteration.

So while the return value of `{$female}` is truthy, this increments `nLooks` by `1`.

The operators lazily evaluates the right operand, and its left operand is optional, defaulting to null. 

## Functions

All functions in Kid are scoped coroutines with one optional parameter and one return value which is null by default.

```kid
plusOne = {args#0 + 1}
```

Functions can access their keys, their neighboring keys in the space they were defined in, and their neighboring keys in the space they get called from.

Here, `plusOne` is assigned a function that returns `args#0 + 1`.

Calling a function is done using the suffix operator `!` with the function's name.

```kid
args = 99
plusOne!
```

To return from a function without resetting its state: without making it start from the top the next time it's called, we use the prefix operator `=>`. This is *the* coroutine feature.

Using `!` on keys of non-function values returns them.

```kid
n = 100
n!
```

Here, `n!` simply returns `100`.

Functions are assigned and passed by reference.

## Time

`**` is a millisecond-level Unix timestamp quantity referring to now. In addition to keeping the time with it, we can use it to wait a number of milliseconds.

```kid
\args: duration
wait = {
	start    = **
	end      = $start + $duration
	
	getOngoing = {** - $start < $duration}
	getOngoing ->> ...
}
```

## Asynchronous Evaluation

Expressions can be evaluated asynchronously if they're enclosed in `[]`. 

```kid
a = 1
[
	duration = 1000
	wait!
	c = 3
]
b = 2
```

Here, `a` and `b` are defined before `c` because `c` was defined asynchronously after `1000` milliseconds.

Asynchronous blocks always return null.

## Networking

We can push a value onto the reception stack of an IP-identified network or computer using the binary operators `<~` and `<~~`.

```kid
2130706433 <~ "Hello!"
1 <~~ {$n + 1}
```

The first operator uses IPv4, and the second one uses IPv6. Thhe left operand is the raw address that corresponds to the IP version.

We can also pop a value from the reception stack using the suffix operator `?`:

```kid
2130706433?
```

A special case of addresses is `0` which don't do anything with data.

## Modules

All modules are global and are stored in one folder. The prefix operator `.` is how we include modules by their names.

```kid
.discord
```

Module names are not the same as their filenames. Just like keys, module names can be of any type (`discord` here is of course just a string). But just like blobs, they also have length limits.

Kid ensures that the same module is never included more than once in a program.
