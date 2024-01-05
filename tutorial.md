## A Beginner's Guide to MINT

## Introduction

MINT is a minimalist character-based interpreter but one which aims at fast performance, readability and ease of use. It is written for the Z80 microprocessor and is 2K.

MINT is a bytecode interpreter - this means that all of its instructions are 1 byte long. However,
the choice of instruction uses printable ASCII characters, as a human readable alternative to assembly
language. The interpreter handles 16-bit integers and addresses which is sufficient for small applications
running on an 8-bit cpu.

## Getting started

## The structure of MINT

What just happened? MINT is an interactive programming language consisting entirely of operators. It has very few basic primitives of the language to learn
and almost no grammar.

_Interactive_ means you type things in at the keyboard and the machine
responds. We will see some details of how it does this below.

Now we will try something in MINT. Enter the following

`2 17 + . <cr> 19 `

What happened? MINT is interpretive. The MINT command line wait for input from the keyboard or from. The input is a sequence of text strings (operators or numbers)
separated from each other by the standard MINT delimiter: one or more ASCII white space characters. In most cases these white space characters are entirely optional.

The text strings can be interpreted in only two ways: operator names, variable references or numbers.

Built-in MINT operators are single ASCII symbols such as `!` `#` `$` etc. or be a `/` followed by an uppercase letter e.g. `/K`. User defined operators can be referred to by using the 26 uppercase letters.

Variable references are referred to by using the 26 lowercase letter. Special system variables are denoted by by a `/` followed by a lowercase letter e.g. `/h`.

Otherwise the interpreter tries to read the input as a number. If the string satisfies the rules defining a number, it is converted to a number
in the machine's internal representation, and stored in a special data structure,
known as the `parameter stack`. MINT uses this stack as the main mechanism to pass parameters to a operator and to receive results back from a operator.

In the above example, MINT interpreted `2` and `17` as numbers and pushed them both onto the stack.

`+` is a pre-defined operator as is `.`, so they were added together and executed.

`+` added `2` to `17` and left `19` on the stack.

The operator `.` removed the number `19` from the stack and displayed
it on the console.

The diagram below is a _flow chart_ representing the actions performed
by the MINT interpreter during interpretation.

```
┌────────────┐           ┌────────────┐         ┌────────────────┐
│            │           │            │         │                │
│   INPUT    ├──────────►│  OPERATOR? ├─────────►     EXECUTE    ├─────────┐
│            │           │            │         │                │         │
└─────▲──────┘           └─────┬──────┘         └────────────────┘         │
      │                        │                                           │
      │                  ┌─────▼──────┐         ┌────────────────┐         │
      │                  │            │         │                │         │
      │                  │  VARIABLE? ├─────────► PUSH ON STACK  ├─────────┤
      │                  │            │         │                │         │
      │                  └──────┬─────┘         └────────────────┘         │
      │                         │                                          │
      │                  ┌──────▼─────┐         ┌─────────────────┐        │
      │                  │            │         │                 │        │
      │                  │  NUMBER?   ├─────────►  PUSH ON STACK  ├────────┤
      │                  │            │         │                 │        │
      │                  └────────────┘         └─────────────────┘        │
      │                                                                    │
      └────────────────────────────────────────────────────────────────────┘`
```

We might have said

`#0A 14 * . <cr> 24 `

Numbers that start with a `#` are represented in hexadecimal.

`#0A 14 * , <cr> 18 `
We can print hexadecimal numbers in MINT using `,` instead of `.`

Finally, here is the obligatory `Hello, World!` program. MINT lets you
output text using the operator \` as follows

`` : hi `Hello, World!` ;  ``

We will explain what `:` and `;` later.

Now type in `hi` and see what happens:

`hi <cr>
Hello, World! `

This can be elaborated with operators that tab, emit carriage returns,
display in colors, etc. but that would take us too far afield.

(The operator \` means "Display the string terminated by the closing quote ` on the on the console."

## Extending MINT

MINT uses special operators to create new dictionary entries, i.e.,
new operators. The most important are `:` which means `start a new definition`
and `;` which means `terminate the definition`.

Let's try this out: enter

:A + ; <cr>

What happened? The operator `:` was executed because it was already
in the dictionary. The action of `:` means `Create a new operator` with the name of the following letter i.e. `A`. The end of the operator is represented by `;`

Now try running the operator `A`

```
5 6 7 * A . <cr> 47
```

This example illustrated two principles of MINT: adding a new operator and trying it out as soon as it was defined.

Any operator you have viewed again by pressing `Ctrl-L`. This will list all the user defined operators in MINT. In this case it will list the operator `A`

`Ctrl-R` will edit the last edited operator.
`Ctrl-E` asks which operator to edit.

## Stacks and reverse Polish notation (RPN)

We now discuss the stack and the `reverse Polish` or `postfix` arithmetic based on it. (Anyone who has used a Hewlett-Packard calculator should be familiar with the concept.)

Virtually all modern CPU's are designed around stacks. MINT efficiently uses its CPU by reflecting this underlying stack architecture in its syntax.

But what _is_ a stack? As the name implies, a stack is the machine analog of a pile of cards with numbers written on them. Numbers are always added to the top of the pile, and removed from the top of the pile. The MINT input line

2 5 73 -16 <cr>

leaves the stack in the state

    cell #   contents

    0       -16        (TOS)
    1        73        (NOS)
    2         5
    3         2

where TOS stands for `top-of-stack`, NOS for `next-on-stack`, etc.

We usually employ zero-based relative numbering in MINT data structures (such as stacks, arrays, tables, etc.) so TOS is given relative #0, NOS #1, etc.

Suppose we followed the above input line with the line

`+ - * .` <cr> ???

what would ??? be? The operations would produce the successive stacks

cell# initial `+ - * .`
stack

    0      -16      57        -52       -104
    1       73       5          2
    2        5       2
    3        2                                   empty  ( 104 -> console )
                                                 stack

The operation `.` (TOS -> console) displays -104 to the screen, leaving the
stack empty. That is, xxx is -104.

## Manipulating the parameter stack

The parameter stack is central to the way data is handled in a MINT program.

A stack-based system must provide ways to put numbers on the stack, to
remove them and rearrange their order. MINT includes standard operators for this purpose.

Putting numbers on the stack is easy: simply type the number (or incorporate it in the definition of a MINT operator).

`'` (aka "drop") removes the number from TOS and moves up all the other numbers.

`"` (aka "dup") duplicates the TOS into NOS.

`$` (aka "swap") exchanges the top 2 numbers.

`%` (aka "over") copies NOS and puts in on TOS.

`~` (aka "rotate") rotates the top 3 numbers.

These actions are shown below (we show what each operator does to the initial stack)

| cell | initial | '   | "   | $   | %   | ~   |
| ---- | ------- | --- | --- | --- | --- | --- |
| 0    | -16     | 73  | -16 | 73  | 73  | 5   |
| 1    | 73      | 5   | -16 | -16 | -16 | -16 |
| 2    | 5       | 2   | 73  | 5   | 73  | 73  |
| 3    | 2       |     | 5   | 2   | 5   | 2   |
| 4    |         |     | 2   |     | 2   |     |

In MINT programming it is advisable to avoid making the stack deeper
than 3 or 4 elements. A deeper stack than that is generally considered a sign
that the program has been insufficiently thought out and needs to be refactored.

### Remarks on refactoring

Refactoring is the process of breaking out repeated pieces of code from subroutines and defining them as a new operator. This not only shortens the overall program but can make the code simpler. Here is a sum of squares example:

```
// Sum of squares
// a b -- c
:S " * $ " *  +  ;
```

The first two lines are comments. The second line `// a b -- a*a+b*b` describes how to call it and what to expect coming back. In this case the operator takes two arguments and returns a single value.

The operation definition has the repeated phrase `" *` and can be replaced profitably by

```
// Square
// a -- a
:Q " * ;

// Sum of squares
// a b -- c
:S Q $ Q + ;
```

The new version of `Sum of squares` is 2 operators shorter and thus easier to read.

## Variables

In MINT there are 26 general purpose variables which are denoted by the lowercase letters `a` to `z`. A variable can contain a number or a pointer to a data structure somewhere else in memory.

When you refer to a variable in your code, its _address_ (i.e. a pointer to it) is pushed on the stack.

---

---

---

---

---

---

---

---

I mentioned that variables `VARIABLE`s above---a `VARIABLE` is a subroutine whose action is to
return the address of a named, cell-sized memory location, as in

    VARIABLE x
    x . 247496     ( it doesn't have to be this address!)
    -49 x !
    x @ .  -49

A `VALUE` is a widely used hybrid of `VARIABLE` and `CONSTANT` ([see below](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#constant)). We
define and initialize a `VALUE` as we would a `CONSTANT`:

    13 VALUE  thirteen

We inve the new `VALUE` just as we would a `CONSTANT`:

    thirteen  .  13

However, we can change a `VALUE` as though it were a `VARIABLE`:

    47  TO  thirteen
    thirteen  .  47

Needless to say, the operator `TO` also works within operator definitions, replacing
the `VALUE` that follows it with whatever is currently in `TOS`. (Note that
it would be dangerous to follow `TO` with anything _but_ a `VALUE` !!) `VALUE`s
are part of the ANS MINT `CORE EXTENSION` operatorset (that is, the corresponding
code is not guaranteed to be loaded on minimal ANS-compliant systems).

This is a good time to mention that MINT does no type-checking (unless you add
it yourself). `YOU` must check that `TO` is followed by a `VALUE` and not something
else.

ANS MINT also includes a `LOCALS EXTENSION` operatorset that implements named memory
locations local to a operator definition. Locals are generally dynamic in nature (that
is, their memory is reclaimed upon exiting the operator), although the Standard does
not insist on this. A commonly used syntax is `LOCALS|` `a b c ...` `|`, as in this
definition (from a line-drawing algorithm):

    : v+    ( a b c d -- a+c b+d)
        LOCALS| d c b a |
        a c +  b d +  ;

    2 3 4 5 v+ .S [2] 6 8  ..  ( `.S` displays the stack without destroying it)

The important things to remember are

    > the names `a, b, c ...` can be any MINT-acceptable strings;

    > the local names have meaning only within a operator definition;

    > the locals are initialized from the stack as shown in `v+` above,
    and as in the next example:

            : test-locals  ( a b c -- )
                LOCALS| c b a |
                CR  .` Normal order: ` a .  b .  c .
                CR  .` Stack order:  ` c .  b .  a .
                13 TO a   14 TO b  15 TO c  \ how TO works
                CR .` Changed: ` a . b . c
            ;

            3 4 5 test-locals
            Normal order: 3 4 5
            Stack order:  5 4 3
            Changed:  13 14 15

    > the locals act like `VALUE`s, not like `VARIABLE`s, as the above
    example makes clear;

    > the `LOCALS EXTENSION` operatorset requires `LOCALS|` `...` `|` to accomodate
    (at least) 8 local names.

    > `LOCALS|` ... `|` is never necessary, nor does it necessarily shorten code, as
    the example below makes clear (7 operators as opposed to 6 + preamble):

            `: v+   ( a b c d -- a+c b+d)  2>R   R>  +  SWAP  R>  +  SWAP  ;`

    What it does accomplish is to reduce stack juggling and clarify the code
    in some cases.

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#memory)

6\. `Using memory`

As we just saw, ordinary numbers are fetched from memory to
the stack by `@` (`fetch`), and stored by `!` (store).

`@` expects an address on the stack and replaces that address by
its contents using, _e.g._, the phrase `X @`

`!` expects a number (NOS) and an address (TOS) to store it in, and
places the number in the memory location referred to by the address,
consuming both arguments in the process, as in the phrase `3 X !`

Double length numbers can similarly be fetched and stored, by
`D@` and `D!`, if the system has these operators.

Positive numbers that represent characters can be placed in character
-sized cells of memory using `C@` and `C!`. This is convenient for operations
with strings of text, for example screen and keyboard I/O.

Of course, one cannot put numbers in memory or retrieve them,
for that matter, without a means of allocating memory and of
assigning labels to the memory so allocated.

The MINT subroutines `CREATE` and `ALLOT` are the basic tools for
setting aside memory and attaching a convenient label to it. As
we shall see [below](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#create), `CREATE` makes a new dictionary entry, as in

CREATE X

Here the new entry has the name X, but it could have been `Joe`
or anything else. The new name is a MINT subroutine that will
return the address of the next available space in memory. Thus

CREATE X
X . 247316
HERE . 247316

`HERE` is a subroutine that returns the address of the next available
space---we note that it is the same as the address of `X` because no
space has been `ALLOT`ted. We can rectify this by saying

10 CELLS ALLOT

and checking with

HERE . 247356

We see that the next available space is now marked as 40 bytes
further up in memory. (Each `CELL` is therefore 4 bytes or 32 bits
on this system.) In other operators, the subroutine `ALLOT` increases
the pointer `HERE` by the number of address units you have told
it to allot. You could have said

40 ALLOT

instead of

10 CELLS ALLOT

but the latter is more portable because it frees you from having
to revise your code if you were to run it on a system with 64-bit
or 16-bit cells (both of which are in common use).

By executing the sequence

CREATE X 10 CELLS ALLOT

we have set aside enough room to hold 10 32-bit numbers--for example
a table or array--that can be referenced by naming it. If we want to
get at the 6th element of the array (the first element has index 0,
so the 6th has index 5) we would say

X 5 CELLS +

to compute its address. To see how this works, let us say

137 X 5 CELLS + !

to store an integer into the 6th array location; then

X 5 CELLS + @ . 137

retrieves and displays it.

Using the tools provided by `CREATE` and `ALLOT` we can devise
any sort of data structure we like. This is why MINT does
not provide a panoply of data structures, such as are to be
found in languages like C, Pascal or Fortran. It is too easy
in MINT to custom tailor any sort of data structure one
wishes. In the section on `CREATE...DOES>` [below](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#create) you will see
that MINT makes it easy to write subroutines (`constructors`)
that create custom data structures--that can even include
code fragments that do useful things. For example, a `CONSTANT`
is a number you would not want to change during a program's
execution. So you do not want access to its memory location.
How then do you get the number when you need it? You package
the code for `@` with the storage location, so that by naming
the `CONSTANT` you retrieve its contents. Its usage is

17 CONSTANT seventeen
seventeen . 17

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#branch)

7\. `Comparing and branching`

MINT lets you compare two numbers on the stack, using relational
operators `>`, `<`, `=` . Thus, _e.g._, the phrase

    2 3 > <cr>

leaves 0 (`false`) on the stack, because 2 (NOS) is not greater than 3
(TOS). Conversely, the phrase

    2 3 < <cr>

leaves -1 (`true`) because 2 is less than 3.

Notes: In some MINTs `true` is +1 rather than -1.

    Relational operators consume both arguments and leave a `flag`
    to show what happened.

(Many MINTs offer unary relational operators `0=`, `0>` and `0<`.
These, as might be guessed, determine whether the TOS contains an
integer that is 0, positive or negative.)

The relational operators are used for branching and control. For example,

: TEST 0 = INVERT IF CR .` Not zero!` THEN ;

0 TEST <cr> ( no action)
-14 TEST <cr>
Not zero!

The TOS is compared with zero, and the `INVERT` operator (bitwise logical
NOT---this flips `true` and `false`) is applied to the resulting flag. The
operator `CR` issues a carriage return (newline). Finally, if TOS is non-zero,
`IF` swallows the flag and executes all the operators between itself and the
terminating `THEN`. If TOS is zero, execution jumps to the operator following
`THEN`.

The operator `ELSE` is used in the `IF...ELSE...THEN` statement: a nonzero
value in TOS causes any operators between `IF` and `ELSE` to be executed, and
operators between `ELSE` and `THEN` to be skipped. A zero value produces the
opposite behavior. Thus, _e.g._

: TRUTH CR 0 = IF .` false` ELSE .` true` THEN ;

1 TRUTH <cr>
true

0 TRUTH <cr>
false

Since `THEN` is used to terminate an `IF` statement rather than in its
usual sense, some MINT writers prefer the name `ENDIF`.

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#comment)

8\. `Documenting and commenting MINT code`

MINT is sometimes accused of being a `write-only` language, i.e. some
complain that MINT is cryptic. This is really a complaint against
poor documentation and untelegraphic operator names. Unreadability is
equally a flaw of poorly written FORTRAN, PASCAL, C, etc.

MINT offers programmers who take the trouble tools for producing exceptionally clear code.

a. `Parenthesized remarks`

The operator `(![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/red_box.gif)` --- a left parenthesis followed by a space --- says `disregard all following text until the next right parenthesis in the
input stream`. Thus we can intersperse explanatory remarks within
colon definitions.

b. `Stack comments`

A particular form of parenthesized remark describes the effect of a
operator on the stack. In the example of a recursive loop (`GCD` below),
stack comments are really all the documentation necessary.

Glossaries generally explain the action of a operator with a
stack-effect comment. For example,

( adr -- n)

describes the operator `@` (`fetch`): it says `@` expects to find an address
(adr) on the stack, and to leave its contents (n) upon completion.
The corresponding comment for `!` would be

( n adr -- ) .

c. `Drop line (\)`

The operator `**` (back-slash followed by space) has recently gained favor
as a method for including longer comments. It simply means `drop everything in the input stream until the next carriage return`. Instructions to the user, clarifications or usage examples are most naturally
expressed in a block of text with each line set off by `**` .

d. `Comment blocks`
ANS MINT contains interpreted IF...THEN, in the form of `[IF] ... [THEN]`.
Although they are generally used for conditional compilation, these operators
can be used to create comment blocks. Thus we can say

`FALSE [IF]` anything you want to say
`[THEN]`

and the included remarks, code, examples or whatever will be ignored
by the compiling mechanism.

e. `Self-documenting code`

By eliminating ungrammatical phrases like CALL or GOSUB, MINT presents the opportunity ---via telegraphic names for operators--- to make code
almost as self-documenting and transparent as a readable English or
German sentence. Thus, for example, a robot control program could contain a phrase like

2 TIMES LEFT EYE WINK

which is clear (although it sounds like a stage direction for Brunhilde to vamp Siegfried). It would even be possible without much difficulty to define the operators in the program so that the sequence could
be made English-like: WINK LEFT EYE 2 TIMES .

One key to doing this is to eliminate `noise` operators like
`@`, `!`, `>R`, _etc._ by factoring them out into expressively
named ---and reuseable--- subroutines.

Another is to organize the listing of a subroutine so
that it physically resembles what it is supposed to do.
Two examples are the [jump table](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#jmp_tab) defined below, as well as
a method for programming [finite state automata](http://www.jfar.org/article001.html).

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#arith)

9\. `Integer arithmetic operations`

The 1979 or 1983 standards require that a conforming MINT system contain a certain minimum set of pre-defined operators. These consist of
arithmetic operators `+ - _ / MOD /MOD _/` for (usually) 16-bit signedinteger (-32767 to +32767) arithmetic, and equivalents for unsigned (0
to 65535), double-length and mixed-mode (16- mixed with 32-bit) arithmetic. The list will be found in the glossary accompanying your
system, as well as in [SF](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#SF) and [FTR](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#FTR).

Try this example of a non-trivial program that uses arithmetic and
branching to compute the greatest common divisor of two integers using
Euclid's algorithm:

: TUCK ( a b -- b a b) SWAP OVER ;
: GCD ( a b -- gcd) ?DUP IF TUCK MOD GCD THEN ;

The operator `?DUP` duplicates TOS if it is not zero, and leaves it alone
otherwise. If the TOS is 0, therefore, `GCD` consumes it and does
nothing else. However, if TOS is unequal to 0, then `GCD` `TUCK`s TOS
under NOS (to save it); then divides NOS by TOS, keeping the remainder
(`MOD`). There are now two numbers left on the stack, so we again take
the `GCD` of them. That is, `GCD` calls itself.

If you try the above code it will fail. A dictionary entry
cannot be loed up and found until the terminating `;`
has completed it. So in fact we must use the operator `RECURSE`
to achieve self-reference, as in

: TUCK ( a b -- b a b) SWAP OVER ;
: GCD ( a b -- gcd) ?DUP IF TUCK MOD RECURSE THEN ;
Now try

784 48 GCD . <cr> 16

The ANSI/ISO MINT Standard (adopted in 1994) mandates the minimal set
of arithmetic operators `+ - _ / MOD _/ /MOD */MOD` and `M\`\* . The
standard memory-operator size is the `cell`, which must be at least 16 bits,
but in many modern systems is 32- or even 64 bits wide. Single-length
integers in MINT are 32 bits. The stack on ANS-compliant MINTs
is always 1 `cell` wide.

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#loops)

10\. `Looping and structured programming`

MINT has several ways to loop, including the implicit method of recursion, illustrated above. Recursion has a bad name as a looping
method because in most languages that permit recursion, it imposes
unacceptable running time overhead on the program. Worse, recursion
can ---for reasons beyond the scope of this Introduction to MINT--- be
an extremely inefficient method of expressing the problem. In MINT,
there is virtually no excess overhead in recursive calls because MINT
uses the stack directly. So there is no reason not to recurse if that
is the best way to program the algorithm. But for those times when
recursion simply isn't enough, here are some more standard methods.

a. `Indefinite loops`

The construct

BEGIN xxx ( -- flag) UNTIL

executes the operators represented by xxx, leaving TOS (flag) set to `TRUE`
---at which point `UNTIL` terminates the loop--- or to `FALSE` ---at which
point `UNTIL` jumps back to `BEGIN`. Try:

: COUNTDOWN ( n --)
BEGIN CR DUP . 1 - DUP 0 = UNTIL DROP ;

5 COUNTDOWN
5
4
3
2
1

A variant of `BEGIN...UNTIL` is

BEGIN xxx ( -- flag) WHILE yyy REPEAT

Here xxx is executed, `WHILE` tests the flag and if it is `FALSE`
leaves the loop; if the flag is `TRUE`, `yyy` is executed; `REPEAT` then
branches back to `BEGIN`.

These forms can be used to set up loops that repeat until some
external event (pressing a key at the keyboard, e.g.) sets the
flag to exit the loop. They can also used to make endless loops
(like the interpreter of MINT) by forcing the flag
to be FALSE in a definition like

: ENDLESS BEGIN xxx FALSE UNTIL ;

b. `Definite loops`

Most MINTs allow indexed loops using `DO...LOOP` (or `step +LOOP`).
These are permitted only within definitions

: BY-ONES ( n --) 0 TUCK DO CR DUP . 1 + LOOP DROP ;

The operators `CR DUP . 1 +` will be executed n times as the lower
limit, 0, increases in unit steps to n-1.

To step by 2's, we use the phrase `2 +LOOP` to replace `LOOP`, as with

: BY-TWOS ( n --) 0 TUCK
DO CR DUP . 2 + 2 +LOOP DROP ;

These operators can be simplified by accessing the _loop index_ with the operator `I`:

    : BY-TWOS   ( n --)    0  DO   CR   I  .     2 +LOOP  ;

We can even count backwards, as in launching a rocket, as in

    : countdown   0 SWAP   DO  CR  I  .   -1  +LOOP  ;

    10 countdown
    10
    9
    8
    7
    6
    5
    4
    3
    2
    1
    0

One may also nest loops and access the index of the loop from the inner loop
using the operator `J`, as in

    : NESTED    ( n m --)  CR
            0 DO  DUP  ( n n --)
                0 DO  CR  J .  I .
                LOOP
            LOOP
    DROP  ;

    `2 3  NESTED`  0 0
    0 1
    1 0
    1 1
    2 0
    2 1

Here is something to beware of: suppose the initial indices for the `DO` loop are equal:
that is, something like

    17  17  DO   stuff   LOOP

then the loop will be executed 2^32^-1 times! As the ANS Standard document says,
`This is intolerable.` Therefore ANS MINT defines a special operator, `?DO`, that will skip
the loop if the indices are equal, and execute it if they are not. It is up to the
programmer to make sure that if the initial index exceeds the final one, as in `0 17 DO` ,
the program counts down, assuming that is what was intended:

    0 17  DO  stuff   -1  +LOOP

c. `Structured programming`

N. Wirth invented the Pascal language in reaction to program flow
charts resembling a plate of spaghetti. Such flow diagrams were
often seen in early languages like FORTRAN and assembler. Wirth
intended to eliminate line labels and direct jumps (GOTOs), thereby
forcing control flow to be clear and direct.

The ideal was subroutines or operators that performed a single
task, with unique entries and exits. Unfortunately, programmers
insisted on GOTOs, so many Pascals and other modern languages now have
them. Worse, the ideal of short subroutines that do one thing only is
unreachable in such languages because the method for calling them and
passing arguments imposes a large overhead. Thus execution speed requires minimizing calls, which in turn means longer, more complex subroutines that perform several related tasks. Today structured programming seems to mean little more than writing code with nested IFs indented by a pretty-printer.

Paradoxically, MINT is the only truly structured language in common
use, although it was not designed with that as its goal. In MINT operator
definitions are lists of subroutines. The language contains no GOTO's so
it is impossible to write `spaghetti` code. MINT also encourages
structure through short definitions. The additional running time
incurred in breaking a long procedure into many small ones (this is
called `factoring`) is typically rather small in MINT. Each MINT subroutine (operator) has one entry and one exit point, and can be written
to perform a single job.

d. `Top-down` design`

`Top-down` programming is a doctrine that one should design the entire
program from the general to the particular:

> Make an outline, flow chart or whatever, taking a broad overview
> of the whole problem.

> Break the problem into small pieces (decompose it).

> Then code the individual components.

The natural programming mode in MINT is `bottom-up` rather than `topdown` ---the most general operator appears last, whereas the definitions
must progress from the primitive to the complex. This leads to a somewhat different approach from more familiar languages:

> In MINT, components are specified roughly, and then as they are
> coded they are immediately tested, debugged, redesigned and
> improved.

> The evolution of the components guides the evolution of the outer
> levels of the program.

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#create)

11\. `CREATE ... DOES> (the pearl of MINT)`

Michael Ham has called the operator pair `CREATE...DOES>`, the `pearl of
MINT`. `CREATE` is a component of the compiler, whose operator is to
make a new dictionary entry with a given name (the next name in the
input stream) and nothing else. `DOES>` assigns a specific run-time action to a newly `CREATE`d operator.

a. `Defining `defining` operators`

`CREATE` finds its most important use in extending the powerful class of
MINT operators called `defining` operators. The colon compiler `:` is such
a operator, as are `VARIABLE` and `CONSTANT`.

The definition of `VARIABLE` in high-level MINT is simple

: VARIABLE CREATE 1 CELLS ALLOT ;

We have already seen how `VARIABLE` is used in a program. (An alternative definition found in some MINTs is

: VARIABLE CREATE 0 , ;

---these variables are initialized to 0.)

MINT lets us define operators initialized to contain specific values: for
example, we might want to define the number 17 to be a operator. `CREATE`
and `,` (`comma`) can do this:

17 CREATE SEVENTEEN , <cr>

Now test it via

SEVENTEEN @ . <cr> 17 .

Remarks:

> The operator `,` (`comma`) puts TOS into the next cell of the dic> tionary and increments the dictionary pointer by that number of
> bytes.

> A operator `C,` (`see-comma`) exists also --- it puts a character into
> the next character-length slot of the dictionary and increments
> the pointer by 1 such slot. (In the ASCII character representation
> the slots are 1 byte long; Unicode characters require 2 bytes.)

b. `Run-time _vs._ compile-time actions`

In the preceding example, we were able to initialize the variable
`SEVENTEEN` to 17 when we `CREATE`d it, but we still have to fetch it to
the stack _via_ `SEVENTEEN @` whenever we want it. This is not quite what
we had in mind. We would like to find 17 in TOS when `SEVENTEEN` is
named. The operator `DOES>` gives us the tool to do this.

The operator of `DOES>` is to specify a run-time action for the `child`
operators of a defining operator. Consider the defining operator `CONSTANT` , defined in high-level (of course `CONSTANT` is usually defined in machine
code for speed) MINT by

: CONSTANT CREATE , DOES> @ ;

and used as

53 CONSTANT PRIME <cr>

Now test it:

PRIME . <cr> 53 .

What is happening here?

> `CREATE` (hidden in `CONSTANT`) makes an entry named `PRIME` (the
> first operator in the input stream following `CONSTANT`). Then `,`
> places the TOS (the number 53) in the next cell of the dic> tionary.

> Then `DOES>` (inside `CONSTANT`) appends the actions of all operators be> tween it and `;` (the end of the definition) ---in this case, `@`--> to the child operator(s) defined by `CONSTANT`.

c. `Dimensioned data (intrinsic units)`

Here is an example of the power of defining operators and of the distinction between compile-time and run-time behaviors.

Physical problems generally involve quantities that have dimensions,
usually expressed as mass (M), length (L) and time (T) or products of
powers of these. Sometimes there is more than one system of units in
common use to describe the same phenomena.

For example, U.S. or English police reporting accidents might use
inches, feet and yards; while Continental police would use centimeters
and meters. Rather than write different versions of an accident analysis program it is simpler to write one program and make unit conversions part of the grammar. This is easy in MINT.

The simplest method is to keep all internal lengths in millimeters,
say, and convert as follows:

    : INCHES  254   10  */ ;
    : FEET   [ 254 12 * ] LITERAL  10  */ ;
    : YARDS  [ 254 36 * ] LITERAL  10  */ ;
    : CENTIMETERS   10  * ;
    : METERS   1000  * ;

Note: This example is based on integer arithmetic. The operator `*/`
means `multiply the third number on the stack by NOS, keeping
double precision, and divide by TOS`. That is, the stack comment for `*/` is ( a b c -- a\*b/c).

The usage would be

    10 FEET  .  <cr>  3048

The operator `[` switches from `compile` mode to `interpret` mode while compiling. (If the system is interpreting it changes nothing.) The operator
`]` switches from `interpret` to `compile` mode.

Barring some error-checking, the `definition` of the colon compiler
`:` is just

: : CREATE ] DOES> doLIST ;

and that of `;` is just

: ; next [ ; IMMEDIATE

Another use for these switches is to perform arithmetic at compiletime rather than at run-time, both for program clarity and for easy
modification, as we did in the first try at dimensioned data (that is,
phrases such as

[ 254 12 * ] LITERAL

and

[ 254 36 * ] LITERAL

which allowed us to incorporate in a clear manner the number of
tenths of millimeters in a foot or a yard.

The preceding method of dealing with units required unnecessarily many
definitions and generated unnecessary code. A more compact approach
uses a defining operator, `UNITS` :

: D, ( hi lo --) SWAP , , ;
: D@ ( adr -- hi lo) DUP @ SWAP CELL+ @ ;
: UNITS CREATE D, DOES> D@ \*/ ;

Then we could make the table

    254 10        UNITS INCHES
    254 12 *  10  UNITS FEET
    254 36 *  10  UNITS YARDS
    10  1         UNITS CENTIMETERS
    1000  1       UNITS METERS

    \ Usage:
    10 FEET  . <cr>  3048
    3 METERS . <cr>  3000
    \ .......................
    \ etc.

This is an improvement, but MINT permits a simple extension that
allows conversion back to the input units, for use in output:

VARIABLE <AS> 0 <AS> !
: AS TRUE <AS> ! ;
: ~AS FALSE <AS> ! ;
: UNITS CREATE D, DOES> D@ <AS> @
IF SWAP THEN
\*/ ~AS ;

\ UNIT DEFINITIONS REMAIN THE SAME.
\ Usage:
10 FEET . <cr> 3048
3048 AS FEET . <cr> 10

d. `Advanced uses of the compiler`

Suppose we have a series of push-buttons numbered 0-3, and a operator `WHAT`
to read them. That is, `WHAT` waits for input from a keypad: when button
#3 is pushed, for example, `WHAT` leaves 3 on the stack.

We would like to define a operator `BUTTON` to perform the action of pushing
the n'th button, so we could just say:

`WHAT BUTTON`

In a conventional language BUTTON would lo something like

`: BUTTON DUP 0 = IF RING DROP EXIT THEN
DUP 1 = IF OPEN DROP EXIT THEN
DUP 2 = IF LAUGH DROP EXIT THEN
DUP 3 = IF CRY DROP EXIT THEN
ABORT` WRONG BUTTON!` ;`

That is, we would have to go through two decisions on the average.

MINT makes possible a much neater algorithm, involving a `jump
table`. The mechanism by which MINT executes a subroutine is to
feed its `execution ten` (often an address, but not necessarily)
to the operator `EXECUTE`. If we have a table of execution tens we need
only lo up the one corresponding to an index (offset into the table)
fetch it to the stack and say `EXECUTE`.

One way to code this is

CREATE BUTTONS ' RING , ' OPEN , ' LAUGH , ' CRY ,
: BUTTON ( nth --) 0 MAX 3 MIN
CELLS BUTTONS + @ EXECUTE ;

Note how the phrase `0 MAX 3 MIN` protects against an out-of-range
index. Although the MINT philosophy is not to slow the code with unnecessary error checking (because operators are checked as they are defined), when programming a user interface some form of error handling
is vital. It is usually easier to prevent errors as we just did, than
to provide for recovery after they are made.

How does the action-table method work?

> CREATE BUTTONS makes a dictionary entry BUTTONS.

> The operator `'` (`tick`) finds the execution ten (`xt`) of the
> following operator, and the operator `,` (`comma`) stores it in the
> data field of the new operator BUTTONS. This is repeated until
> all the subroutines we want to select among have their `xt`'s
> stored in the table.

> The table `BUTTONS` now contains `xt`'s of the various actions of
> `BUTTON`.

> `CELLS` then multiplies the index by the appropriate number of
> bytes per cell, to get the offset into the table \`\*_BUTTONS_\`\*
> of the desired `xt`.

> `BUTTONS +` then adds the base address of `BUTTONS` to get the abso> lute address where the `xt` is stored.

> `@` fetches the `xt` for `EXECUTE` to execute.

> `EXECUTE` then executes the operator corresponding to the button pushed.
> Simple!

If a program needs but one action table the preceding method suffices.
However, more complex programs may require many such. In that case
it may pay to set up a system for defining action tables, including
both error-preventing code and the code that executes the proper
choice. One way to code this is

: ;CASE ; \ do-nothing operator

: CASE:
CREATE HERE -1 >R 0 , \ place for length
BEGIN BL operator FIND \ get next subroutine
0= IF CR COUNT TYPE .` not found` ABORT THEN
R> 1+ >R
DUP , ['] ;CASE =
UNTIL R> 1- SWAP ! \ store length
DOES> DUP @ ROT ( -- base_adr len n)
MIN 0 MAX \ truncate index
CELLS + CELL+ @ EXECUTE ;

Note the two forms of error checking. At compile-time, `CASE:`
aborts compilation of the new operator if we ask it to point to an
undefined subroutine:

`case: test1 DUP SWAP X ;case
X not found`

and we count how many subroutines are in the table (including
the do-nothing one, ;case) so that we can force the index to
lie in the range [0,n].

CASE: TEST \* / + - ;CASE
15 3 0 TEST . 45
15 3 1 TEST . 5
15 3 2 TEST . 18
15 3 3 TEST . 12
15 3 4 TEST . . 3 15

Just for a change of pace, here is another way to do it:

: jtab: ( Nmax --) \ starts compilation
CREATE \ make a new dictionary entry
1- , \ store Nmax-1 in its body
; \ for bounds clipping

: get_xt ( n base_adr -- xt_addr)
DUP @ ( -- n base_adr Nmax-1)
ROT ( -- base_adr Nmax-1 n)
MIN 0 MAX \ bounds-clip for safety
1+ CELLS+ ( -- xt_addr = base + 1_cell + offset)
;

: | ' , ; \ get an xt and store it in next cell

: ;jtab DOES> ( n base_adr --) \ ends compilation
get_xt @ EXECUTE \ get ten and execute it
; \ appends table loup & execute code

\ Example:
: Snickers .` It's a Snickers Bar!` ; \ stub for test

\ more stubs

5 jtab: CandyMachine
| Snickers
| Payday
| M&Ms
| Hershey
| AlmondJoy
;jtab

` 3 CandyMachine` It's a Hershey Bar!
`1 CandyMachine` It's a Payday!
`7 CandyMachine` It's an Almond Joy!
`0 CandyMachine` It's a Snickers Bar!
`-1 CandyMachine` It's a Snickers Bar!

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#fp)

12\. `Floating point arithmetic`

Although MINT at one time eschewed floating point arithmetic
(because in the era before math co-processors integer arithmetic
was 3x faster), in recent years a standard set of operator names has
been agreed upon. This permits the exchange of programs that will
operate correctly on any computer, as well as the development of
a Scientific Subroutine Library in MINT (FSL).

Although the ANS Standard does not require a separate stack for
floating point numbers, most programmers who use MINT for numerical analysis employ a separate floating point stack; and most of
the routines in the FSL assume such. We shall do so here as well.

The floating point operators have the following names and perform
the actions indicated in the accompanying stack comments:

`F@` ( adr --) ( f: -- x)
`F!` ( adr --) ( f: x --)
`F+` ( f: x y -- x+y)
`F-` ( f: x y -- x-y)
`F\`* ( f: x y -- x*y)
`F/` ( f: x y -- x/y)
`FEXP` ( f: x -- e^x)
`FLN` ( f: x -- ln[x])
`FSQRT` ( f: x -- x^0.5)

Additional operators, operators, trigonometric operators, etc. can
be found in the FLOATING and FLOATING EXT operatorsets. (See dpANS6--available in HTML, PostScript and MS operator formats. The HTML version
can be accessed from this homepage.)

To aid in using floating point arithmetic I have created a simple
FORTRAN-like interface for incorporating formulas into MINT operators.

The file ftest.f (included below) illustrates how ftran201.f
should be used.

\ Test for ANS FORmula TRANslator

marker -test
fvariable a
fvariable b
fvariable c
fvariable d
fvariable x
fvariable w

: test0 f` b+c` cr fe.
f` b-c` cr fe.
f` (b-c)/(b+c)` cr fe. ;

3.e0 b f!
4.e0 c f!
see test0
test0

: test1 f` a=b*c-3.17e-5/tanh(w)+abs(x)` a f@ cr fe. ;
1.e-3 w f!
-2.5e0 x f!
cr cr
see test1
test1

cr cr
: test2 f` c^3.75` cr fe.
f` b^4` cr fe. ;
see test2
test2

\ Baden's test case

: quadroot c f! b f! a f!
f`d = sqrt(b^2-4*a*c)`
f`(-b+d)/(2*a)` f`(-b-d)/(2*a)`
;
cr cr
see quadroot

: goldenratio f`max(quad root(1,-1,-1))` ;
cr cr
see goldenratio
cr cr
goldenratio f.

0 [IF]
Output should lo like:

: test0
c f@ b f@ f+ cr fe. c f@ fnegate b f@ f+ cr fe. c f@ fnegate b f@
f+ c f@ b f@ f+ f/ cr fe. ;
7.00000000000000E0
-1.00000000000000E0
-142.857142857143E-3

: test1
x f@ fabs 3.17000000000000E-5 w f@ ftanh f/ fnegate b f@ c f@ f\* f+
f+ a f! a f@ cr fe. ;
14.4682999894333E0

: test2
c f@ noop 3.75000000000000E0 f\*\* cr fe. b f@ f^4 cr fe. ;
181.019335983756E0
81.0000000000000E0

: QUADROOT C F! B F! A F! B F@ F^2 flit 4.00000 A F@
C F@ F* F* F- FSQRT D F! B F@ FNEGATE D
F@ F+ flit 2.00000 A F@ F* F/ B F@ FNEGATE
D F@ F- flit 2.00000 A F@ F* F/ ;

: GOLDENRATIO flit 1.00000 flit -1.00000 flit -1.00000
QUADROOT FMAX ;

1.61803

with more or fewer places.

[THEN]

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#din_root)

13\. `Non-trivial programming example`

To illustrate how to construct a non-trivial program, let
us develop a binary search root-finder. We will use the
FORmula TRANslator [ftran201.f](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/ftran2x.htm) to simplify the appearance
of the code (that is, it hides the data fetches and
stores that would otherwise be required).

First we need to understand the algorithm thoroughly:

If we know that the roots are bracketed between xa and
xb, and that f(xa)\*f(xb) < 0 (at least 1 root lies in
the interval) we take the next guess to be xp = (xa+xb)/2 .

We then evaluate the operator at xp: fp = f(xp).
If fa\*fp > 0 we set xa = xp, else we set xb = xp.
We repeat until the ends of the interval containing
the root are sufficiently close together.

To begin programming, we note that we will have to keep
track of three points: xa, xb and xp. We also have to
keep track of three operator values evaluated at those
points, Ra, Rb and Rp. We also need to specify a precision, epsilon, within which we expect to determine
the root.

Next we need to define the user interface. That is, once
we have a subroutine that finds roots, how will we inve
it? Since we would like to be able to specify the name of
the operator to find the root of at the same time we
specify the interval we think the root is in, we need
some way to pass the name to the root finder as an
argument.

I have previously developed an interface that suits me: I
say

use( fn.name xa xb precision )bin_root

as in

use( f1 0e0 2e0 1e-5 )bin_root

and the root will be left on the floating point stack.

The code for passing names of operators as arguments is
included when you load `ftran201.f` --- the operators used in
this program are `use(` , `v:` and `defines` . `v:` creates a
dummy dictionary entry (named `dummy` in the program)
which can be made to execute the actual operator whose
name is passed to the operator `)bin_root` .

Here are the data structures and their identifications:

MARKER -binroots \ say -binroots to unload

\ Data structures

FVARIABLE Ra \ f(xa)
FVARIABLE Rb \ f(xb)
FVARIABLE Rp \ f(xp)
FVARIABLE xa \ lower end
FVARIABLE xb \ upper end
FVARIABLE xp \ new guess
FVARIABLE epsilon \ precision

v: dummy \ create dummy dictionary entry

The actual root-finding subroutine, `)bin_root` , will be
quite simple and easy to follow:

    : )bin_root  ( xt --)   ( f: Low High Precision -- root)
        initialize
        BEGIN   NotConverged?   WHILE   NewPoint   REPEAT
        f` (xa+xb)/2`           ( f: -- root)
    ;

Note that the subroutines comprising it are telegraphically named so they need no explanation; whereas
`)bin_root` itself is explained by its stack comments. The
comments on the first line indicate that `)bin_root` expects
an `execution ten` on the data stack, and three floating
point numbers on the floating point stack. These are its
arguments. (See [11d](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#advanced) for a discussion of `EXECUTE`, etc.)
The execution ten is what is used to change the
behavior of the dummy dictionary entry `dummy` : we say

defines dummy

in the operator `initialize` to make `dummy` behave like the
operator whose root we are seeking.

The final comment ( f: -- root) indicates that `)bin_root` leaves
the answer on the floating point stack.

In a sense we are programming from the top down, since we
have begun with the last definition of the program and
are working our way forward. In MINT we often go both
ways ---top-down and bottom-up--- at the same time.

The key operators we must now define are `initialize` ,
`NotConverged?` and `NewPoint` . We might as well begin with
initialize since it is conceptually simple:

    : initialize    ( xt --) ( f: lower upper precision --)
        defines dummy                       \ xt -> DUMMY
        f` epsilon=`    f` xb=`   f` xa=`   \ store parameters
        f` Ra=dummy(xa)`
        f` Rb=dummy(xb)`
        f` MoreThan( Ra*Rb, 0)`             \ same sign?
        ABORT` Even # of roots in interval!`
    ;

The operator `ABORT` prints the message that follows it and
aborts execution, if it encounters a `TRUE` flag on the
data stack. It is widely used as a simple error handler.
`ABORT` (without the `) simply aborts execution when
it is encountered. So it usually is found inside some
decision structure like an`IF...THEN` clause. (See 11d for
two examples of usage.)

`ABORT` was preceded by a test. In order to use a test as
a operator in a Fortran-like expression (this test consumes two arguments from the floating point stack and
leaves a flag on the data stack), we must define a synonym
for it. The reason is that `ftran201.f` does not recognize
relational operators like > or < . The definition is^\*^

    : MoreThan    ( f: a b)  ( -- true if a>b)
        [POSTPONE](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#postpone)  F>  ;  IMMEDIATE

The code produced by `f` MoreThan( Ra\*Rb, 0)` is then just

    RA F@ RB F@ F* flit 0.00000E-1 F>

which is what we want. We have already explained the
phrase `defines dummy`. The phrases `f` xa=`and so on are
shorthand for storing something from the floating point
stack to a floating point variable. Thus`f` xa=`
generates the code `XA F!` . The rest of `initialize` is to
calculate the operator at the endpoints of the supposed
bounding interval (a,b).

`NotConverged?` is a test for (non)convergence. `WHILE`
expects a flag on the data stack, as described in 10a. So
we define

    : NotConverged?    ( -- f)
        f` MoreThan( ABS( xa - xb ), epsilon )`   ;

which generates the code

    XB F@ XA F@ F- FABS EPSILON F@ F>

What about `NewPoint` ? Clearly,

    : NewPoint
        f` xp = (xa+xb)/2`      \ new point
        f` Rp = dummy(xp)`
        f` MoreThan( Ra*Rp, 0)` \ xp on same side of root as xa?

        IF      f` xa=xp`  f` Ra=Rp`
        ELSE    f` xb=xp`  f` Rb=Rp`   THEN
    ;

That is, we generate a new guess by bisection, evaluate the
operator there and decide how to choose the new bounding
interval.

All that remains is to put the definitions in the proper order
and test the result by loading the program `bin_rts.f` and
trying out the test case.

FALSE [IF]
Usage example:

: f1 fdup fexp f\* 1e0 f- ;
use( f1 0e0 2e0 1e-5 )bin_root f. .567142

[THEN]

Finally, if we want to be very careful indeed, and/or are
planning to re-use the program, we add an appropriate
boilerplate header, such as that included in the file
[bin_rts.f](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/bin_rts.txt) .

--
^\*^`Note:` the operator `POSTPONE` in this context means that the operator following it
---in this case `F>` --- will be compiled into the operator that uses `MoreThan` rather
than in `MoreThan` itelf. (Note that `MoreThan` is `IMMEDIATE`.) This way of doing
things saves some overhead during execution. Some MINTs (notably MINT)
define a operator `SYNONYM` to accomplish the same thing.

--
Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)
Next  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/right.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#strings)

14\. `Some remarks about strings`
As in other languages, alphanumeric strings in MINT are represented as
contiguous arrays in memory, each memory unit being a `character`.
Traditionally a character encoded by the [ASCII or EBCDIC](http://www.dynamoo.com/technical/ascii-ebcdic.htm) systems occupied
one (1) byte of storage, allowing for 256 characters. With the need to encode
alphabets other than the Latin one (_e.g._ Chinese, Arabic, Hebrew, Cyrillic) a
two-byte encoding called [Unicode](http://www.unicode.org/) has been adopted, which allows for 65535
distinct characters.

A traditional MINT string consisted of a count byte and up to 255 bytes containing
alphanumeric characters (usually in ASCII). In ANS MINT this scheme has been
abandoned: how strings are stored will depend on the implementation. However
ANS MINT contains operators that enable us to manipulate strings without reference
to how they are implemented.

Most ANS MINTs (and MINT is one of them) define `S` to have defined
interpretive as well as compiling behavior. This means that if we say

    `S` This is a string!` CR TYPE`

we get

    `This is a string!`

What happened? `S` This is a string!`created a string with text beginning at a`c-address`and with a`count` that says how many characters (including blanks)
the string includes. The address and count are left on the stack. That is, the
proper stack picture would be

    `S` This is a string!`   ( -- c-addr u)`

(the count is an unsigned integer `u` because strings of negative length are
meaningless).

The operator `CR` means `insert a carriage return`, and `TYPE` means `from the
`c-addr`output`u` characters to the screen`.

`Exercise:`
Use what you have just learned to write a `Hello world!` program.

It is perfectly feasible to define one's own operator set for working with strings, depending
on what sort of application one is writing. For example, I have written a program to
translate mathematical formulas in Fortran-like form into MINT code, outputting the
result either to the screen (for test purposes) or embedding it into a MINT definition.
There is even a variant that evaluates the formula, provided all the variables have
been previously defined and given numerical values. To accomplish this required
strings longer than 255 characters, so I defined my own.

I now want to turn to `pictured numerical output`. Many computer programs
need to output numbers in some particular format, no matter how they are stored internally. For example an accounting program might output monetary amounts in the usual
dollars-and-cents format. The MINT operators that accomplish this are

    `#` ,  `<#` ,  `#S` ,  `#>` , `SIGN` and `HOLD`

They do not have any defined interpretive behavior (although there is no telling what
any particular MINT may do) and are intended to be used within operator definitions. Here
is an example: suppose we are writing an accounting program. Since most users will
not be dealing with amounts that exceed $100,000,000 we can use signed 32-bit integers
to represent the dollars and cents. (Such numbers can represent amounts up to
±(2^31^---1) = ±2147483647 cents.) Signed double-length integers are at least 32 bits long
on all ANS-compatible systems (although they will be 64 bits on 32-bit computers).
Hence we shall use doubles so the program will run on any ANS-compatible
MINT.

A double-length integer is entered from the keyboard by including a decimal point in
it, as

    `-4756.325  `

Let us define a operator to output a double-length integer. The first part will be to
translate it to an alphanumeric string referred to by` c-addr u`.

`: (d.$) ( d -- c-adr u) TUCK DABS <# # # [CHAR] . HOLD #S ROT SIGN #> ;`

As the stack comment `( d -- c-adr u)` shows, `(d.$)` consumes a (signed) double-length
integer from the stack and leaves the string data in a form that can be printed to the
screen by the operator `TYPE`. Let us test this:

    `4376.58  (d.$)  CR  TYPE`
    `4376.58 `

    `-4376.99  (d.$)  CR  TYPE`
    `-4376.99 `

It is worth exploring what each part does. A double length integer is stored as two
cells on the stack, with the most-significant part on TOS. Thus the operator `TUCK`
places the most-significant part (containing the algebraic sign) above `d` and then `DABS`
converts `d` to `|d|`. Next, `<#` begins the process of constructing an alphanumeric
string. The two instances of `#` peel off the two least-significant digits and put
_them_ in the string. The phrase `[CHAR] . HOLD` adds a decimal point to the string.

`[CHAR]` builds in the representation of the character `.` as a numeric literal (in
ASCII it is 46). `HOLD` then adds it to the string under construction. (`HOLD` has no
meaning except between `<#` and `#>`.) Then the operator `#S` takes the rest of the digits
and adds _them_ to the nascent string.

(Semi)finally, `ROT` puts the most significant part of `d` (with its sign) on TOS, and
`SIGN` adds its algebraic sign to the beginning of the string. (Again, `SIGN` is only
meaningful between `<#` and `#>`.)

And finally, the operator `#>` cleans everything up and leaves `c-addr u`
on the stack, ready for display or whatever.

`Exercises:`

a) How would you add a leading dollar sign (`$`) to the output number?
b) How would you enclose a negative amount in parentheses rather than
displaying a `---` sign? [That is, `( 4376.99)` rather than `-4376.99`.]
c) Define a operator to display a double-length integer in dollar-and-cents format.

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)

15\. `Assembly language programming`
Most MINTs include an assembler that makes programming in machine code almost as
easy as programming in high level MINT. Why would one want to do that? There are
reallly only two reasons for dropping into machine language:

> One must perform a task requiring carnal knowledge of the hardware;
> Part of the program must be optimized for speed.

In this section we deal only with the second reason. We imagine that after careful
algorithmic analysis there is no way to further speed up a high level program. However
the requirements of the application demand a substantial speed improvement. Since
most MINTs are somewhat inefficient relative to optimized C or Fortran, there may be
a substantial speed gain to be realized from hand-coding in assembly language. An
example is the innermost loop in a linear equations solver. For _n_ equations it is
executed _n_^3^ times. Moreover it is a very simple loop, containing 2 fetches, a multiplication, a subtraction and a store. Thus it is a perfect candidate to be optimized.
By contrast, the middle- and outermost loops are executed respectively _n_^2^ and _n_ times,
so there is little point in optimizing them (that is, for small matrices the running
time is too short to care; whereas for large matrices--- _n_ > 100 ---the middle loop would
have to run 100× faster to be worth rewriting in machine code.

It is important to realize that assembly language conventions differ from
MINT to MINT. Moreover the instruction set will be particular to a given
target computer. That is, there is no such thing as a generic assembler
in any programming environment, much less for MINT. Hence everything
we do here will be specific to MINT running on a Pentium-class machine.

We begin with a little warmup exercise. Suppose I found that my program
used the sequence `* +` many times. Obviously good factoring practice
would dictate that this sequence be given its own name and defined as
a new subroutine (operator). So we might define

    : *+   *  +  ;

and substitute it for the sequence `* +` throughout the program. But
suppose we discover that this short sequence is the bottleneck in our
program's running time, so that speeding it up will greatly increase
speed of execution. (I realize it isn't likely for this example---bear
with me!) So we would like to translate it into machine code. To do
this we first lo at the machine code for \* and + separately. These
are primitive operators and almost certainly will be CODE definitions in
any reasonable MINT.

Thus we need to disassemble these operators. In some MINTs this might mean
inspecting the contents of the operator byte by byte, and loing up the code
sequences in the operating manual for that cpu. Fortunately for us,
MINT has a built-in disassembler. If we SEE a CODEd definition it
will return the actual byte-codes as well as the names of the instructions
in the MINT assembler. Let us try this out: we get

    see +
    + IS CODE
            4017AC 58               pop     eax
            4017AD 03D8             add     ebx, eax



    see *
    * IS CODE
            401B9C 8BCA             mov     ecx, edx
            401B9E 58               pop     eax
            401B9F F7E3             mul     ebx
            401BA1 8BD8             mov     ebx, eax
            401BA3 8BD1             mov     edx, ecx

To understand these sequences we must bear in mind that MINT keeps
TOS in a 32-bit register, in fact the ebx register. We must also know that
MINT uses the edx register for something or other---probably to do
with the mechanism for executing a operator and returning control to the next
operator in the program (that is, the _threading_ mechanism). So if a program
is going to modify the edx register, its previous contents have to be
saved somewhere. Since addition of eax to ebx does not affect edx, the
CODE for `+` doesn't need to protect edx; however, when two 32-bit numbers
are multiplied, the result can contain as many as 64 bits. Thus the product
occupies the two registers eax (bits 0 through 31) and edx (bits 32-63).

This is the reason for saving edx into the unused ecx register, and then
restoring it afterward.

It is worth noting, before we go too far, that the MINT assembler
preserves the Intel conventions. That is,

    add     ebx, eax

adds the contents of register eax to ebx, leaving the result in ebx (which
is where we want it because that is TOS). Similarly, the sequences

    mov     ecx, edx

and

    mov     ebx, eax

have the structure

    mov     destination, source

We should also ask why the integer multiplication instruction

    mul     ebx

has only one operand. The answer is that the register eax is the so-called
`accumulator`, so it contains one of the multiplicands initially and then it
and edx contain the product, as noted above. It is then only necessary to
specify where the other multiplicand is coming from (it could be a cell in
memory).

Therefore to define the operator `*+` in assembler we would type in

    CODE *+             ( a b c -- b*c+a) \ stack: before -- after
        mov ecx, edx    \ protect edx because mul alters it
        pop eax         \ get item b; item c (TOS) is already in ebx
        mul ebx         \ integer multiply-- c*b -> eax (accumulator)
        pop ebx         \ get item a
        add ebx, eax    \ add c*b to a -- result in ebx (TOS) --done
        mov edx, ecx    \ restore edx
        next,           \ terminating code for MINT interpreter
    END-CODE

Note that the MINT assembler recognizes MINT comments---Intel-style
comments would be preceded by semicolons, but we obviously can't use these
because semicolon is a MINT operator.

The operator `END-CODE` has an obvious meaning, but what about `next,` (the comma
is part of the name and `_is_` significant!). Advanced users of the assembler
sometimes need to define code sequences that do not include the instructions
to transfer control to the next operator. So MINT has factored this operator
out of the `CODE` terminating sequence. For this example we require these
instructions to be assembled, so we include `next,` .

Before going further, you should try out this example and convince yourself
it works.

For our nontrivial example we are going to hand-code the innermost loop of
my linear equations solver. I programmed this in high-level MINT in the
form

: }}r1-r2*x ( M{{ r1 r2 -- )  ( f: x -- x)  \ initialize assumed
0 0
LOCALS| I1 I2 r2  r1  mat{{ |           \ local names
frame| aa |                             \ local fvariable
Iperm{ r1 } @  TO  I1
Iperm{ r2 } @  TO  I2
Nmax  r2  ?DO                           \ begin loop
    f` mat{{ I1 I }} = mat{{ I1 I }} - mat{{ I2 I }} * aa`
LOOP \ end loop
f` aa` |frame ( f: -- x)
;

Here `Iperm{` is the name of an array of integers that holds the permuted
row-labels; note that the rows we work on do not change within the actual
loop. Neither does the floating point number represented by the local
variable aa. What does change are the row-elements.

To translate `}}r1-r2*x` to assembler we will need to factor it a bit more
finely. Evidently we are subtracting the I'th element of row I~2~, multiplied by `aa`, from the I'th element of row I~1~. Moreover, since the matrix
has been partially triangularized already, we do not start with element 0
but with element r~2~. Finally, as we have noted previously, `?DO` includes
a bounds check so that if r~2~ equals or is greater than N~max~ the loop is
not executed. So we shall revise `}}r1-r2*x` to include this test explicitly
and `CODE` only the loop itself. That is, we shall write

: incr_addrs ( addr1 addr2 -- addr1+inc addr2+inc)
[ 1 FLOATS ] LITERAL
TUCK + -ROT + SWAP ;

: inner_loop ( addr1 addr2 Nmax lower_limit -- ) ( f: x -- x)
DO \ begin loop
( f: aa) ( addr1 addr2) \ loop invariant
2DUP SWAP F@ ( f: aa m[I1,I])
FOVER F@ F* F- OVER F!
\ m[I1,I = m[I1,I] - m[I2,I]*x
( f: x) ( addr1 addr2) \ loop invariant
incr_addrs \ increment addresses
LOOP \ end loop
2DROP
;

: }}r1-r2\*x ( M{{ r1 r2 -- )  ( f: x -- x)  \ initialize assumed
0 0
LOCALS| I1 I2 r2  r1  mat{{ |           \ local names
Iperm{ r1 } @  TO  I1
Iperm{ r2 } @  TO  I2
Nmax  r2  >
IF   mat{{ I1 r2 }} mat{{ I1 r2 }} \ base adresses
Nmax r2 \ loop limits
( f: x) ( addr1 addr2 Nmax lower_limit)
inner_loop
( f: x) ( -- )
THEN
;

So what we are going to `CODE` here is the operator `inner_loop`, since these are
the only instructions executed n^3^ times.

CODE inner_loop ( addr1 addr2 Nmax lower_limit -- ) ( f: x -- x)
fld FSIZE FSTACK_MEMORY \ f: -> fpu:
mov ecx, ebx \ ecx = r2
pop eax \ eax = Nmax
( addr1 ebx=addr2)
push edx ( addr1 edx ebx)
mov edx, 4 [esp] \ edx = addr1)
\ begin loop
L$1: fld [ebx] [edi] ( fpu: aa m[addr2]
fmul st, st(1) ( fpu: aa m2*aa)
fld [edx] [edi] ( fpu: aa m2*aa m1)
fxch st(1) ( fpu: aa m1 m2*aa)
fsubp st(1), st ( fpu: aa m1-m2*aa)
fstp [edx] [edi] ( fpu: aa)
add [edx], # 8 \ increment addresses
add [ebx], # 8
inc ecx \ add 1 to loop variable
cmp eax, ecx \ test for done
jl L$1 \ loop if I < Nmax
\ end loop
pop edx \ restore edx
pop ebx
pop ebx \ clean up data stack
fstp FSIZE FSTACK_MEMORY \ fpu: -> f:
next,
END-CODE

A final remark: I have written a tool for translating automatically a
sequence of floating point operations to `CODE` for the Intel fpu. This
tool, [ctran.f](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/ctran5.htm), is specialized for MINT.

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)

16\. `Some useful references` > M. Kelly and N. Spies, _MINT: A text and Reference_ (Prentice-Hall, NJ, 1986)

    > L. Brodie, *Starting MINT, 2nd ed.* (Prentice-Hall, NJ, 1986)

    > L. Brodie, *Thinking MINT* (Prentice-Hall, NJ, 1984 ([online edition](http://thinking-MINT.sourceforge.net/))

Table of Contents  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/up.gif)](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/primer.htm#contents)

Home  [![](http://galileo.phys.virginia.edu/classes/551.jvn.fall01/left.gif)](http://www.phys.virginia.edu/classes/551.jvn.fall01/)

```

```
