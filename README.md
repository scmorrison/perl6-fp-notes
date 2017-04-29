# Notes on functional programming with Perl 6

### Table of Contents

* __[Prelude](#prelude)__
* __[Notes](#notes)__
    * [Immutability](#immutability)
* __[Getting Involved](#getting-involved)__
* __[Copying](#copying)__
    * [License](#license)
    * [Attribution](#attribution)

## Prelude

[Perl 6] is a multi-paradigm programming language that offers several features that make writing in a functional style simple, most of the time. Perl 6 is not a _*pure*_ functional programming language and shouldn't be treated as such. This repository was created to document various FP solutions and approaches using Perl 6.

**Note**: This is not to be considered the **one_true_way_to_write_perl_6** in any paradigm.

## Notes

### Immutability

* See the following articles regarding Immutability in Perl 6:
  * [Perl 6 Information Model]
  * [Rosetta Code - Enforced immutability#Perl 6]

* The following built-in types are considered immutable: 

| Type          | Description                                                  |
|---------------|--------------------------------------------------------------|
|**Str**        | Perl string (finite sequence of Unicode characters)          |
|**Int**        | Perl integer (allows Inf/NaN, arbitrary precision, etc.)     |
|**Num**        | Perl number (approximate Real, generally via floating point) |
|**Rat**        | Perl rational (exact Real, limited denominator)              |
|**FatRat**     | Perl rational (unlimited precision in both parts)            |
|**Complex**    | Perl complex number                                          |
|**Bool**       | Perl boolean                                                 |
|**Exception**  | Perl exception                                               |
|**Block**      | Executable objects that have lexical scopes                  |
|**Seq**        | A list of values (can be generated lazily)                   |
|**Range**      | A pair of Ordered endpoints                                  |
|**Set**        | Unordered collection of values that allows no duplicates     |
|**Bag**        | Unordered collection of values that allows duplicates        |
|**Enum**       | An immutable Pair                                            |
|**Map**        | A mapping of Enums with no duplicate keys                    |
|**Signature**  | Function parameters (left-hand side of a binding)            |
|**Capture**    | Function call arguments (right-hand side of a binding)       |
|**Blob**       | An undifferentiated mass of ints, an immutable Buf           |
|**Instant**    | A point on the continuous atomic timeline                    |
|**Duration**   | The difference between two Instants                          |
|**HardRoutine**| A routine that is committed to not changing                  |

* Use the bind `:=` infix for scalars, which defaults to 'ro':

  ```perl6
  my $name := "Camelia";
  $name = "Elaine";
  
  Cannot assign to an immutable value
    in block <unit> at <unknown file> line 1
  ```

* Lists without containers are `ro` immutable, use Lists instead of Arrays (@) for listy things:

  ```perl6
  my $list = (1, 2, 3);
  push $list, 4;
  Cannot call 'push' on an immutable 'List'
    in block <unit> at <unknown file> line 1
  ```

  > A list is immutable, which means you cannot change the number of elements in a list. But if one of the elements happens to be a scalar container, you can still assign to it. [[1]]

  In this example `$list[0]` is a `rw` container, `$x`, with a value of `42`. This will allow replacing the value inside the `$x` scalar container:

  ```perl6
  my $x = 42;
  my $list = ($x, 1, 2);
  $list[0] = 23;
  say $x;                 # 23
  ```

  Instead, bind the `Int` value to `$x` which which creates a `ro` container:

  ```perl6
  my $x := 42;
  my $list = ($x, 1, 2);
  $list[0] = 23;     # Error
  Cannot modify an immutable Int
    in block <unit> at <unknown file> line 1
  ```

  This example tries replacing the value of `$list[1]`, which is an immutable `Int`:

  ```perl6
  my $x = 42;
  my $list = ($x, 1, 2);
  $list[1] = 23;     # Error
  Cannot modify an immutable Int
    in block <unit> at <unknown file> line 1
  ```

  Use Array (@) in subroutine signatures for FP-style list processing of slurpy subparams. Lists will not work here. The new `@` Array is `ro` immutable within the scope of the subroutine:

  ```perl6
  multi sub do-sum([$head, *@tail] ) { do-sum( $head, @tail ); };
  multi sub do-sum($total, [$head, *@tail] ) { do-sum( $total + $head, @tail ); };
  multi sub do-sum($total, [] ) { $total; };

  my List $list = (1,2,3,4);
  do-sum($list);

  # Attempting to modify an subparameter Array inside its subrouting
  sub edit-param(($head, *@tail)) {
    @tail[2] = 30;  # Error
    return ($head, @tail).flat;
  };

  say edit-param((1,2,3,4));
    Cannot modify an immutable Int
  ```

  Use Map for associative Arrays / Hash like behavior:

  ```perl6
  my Map $list = (name => "Camelia", email => "camelia@perl6.tld").Map
  Map.new((:email("camelia\@perl6.tld"),:name("Camelia")))
  say $list<name>;
  Camelia

  # Try modifying $list<name>
  $list<name> = "Steve";
  Cannot modify an immutable Str
    in block <unit> at <unknown file> line 1

  # Try adding a new key-value pair
  $list<email> = "camelia@perl6.tld";
  Cannot modify an immutable Mu
    in block <unit> at <unknown file> line 1
  ```

## Getting Involved

When you submit a PR and it gets merged, you will be automatically added as a collaborator, but if you wouldn't like to be added, please mention it in your submission.

The larger Perl 6 community is very approachable and does a great job of answering questions and helping those new to Perl 6. If you're looking for other projects to contribute to please see the [Perl 6 Most Wanted Modules][Perl 6 Most Wanted].

## Copying

### License

![Creative Commons License](http://i.creativecommons.org/l/by/3.0/88x31.png)
This work is licensed under a
[Creative Commons Attribution 3.0 Unported License][license]

### Attribution

https://github.com/scmorrison/perl6-fp-notes/graphs/contributors

<!-- Links -->
[FP Notes on Perl 6 (this repo)]: https://github.com/scmorrison/perl6-fp-notes
[Perl 6]: http://perl6.org
[Perl 6 Most Wanted]: https://github.com/perl6/perl6-most-wanted
[Perl 6 Information Model]: http://www.dlugosz.com/Perl6/web/info-model-1.html
[Rosetta Code - Enforced immutability#Perl 6]: https://rosettacode.org/wiki/Enforced_immutability#Perl_6
[1]: https://docs.perl6.org/language/containers.html#Scalar_containers_and_listy_things
[license]: http://creativecommons.org/licenses/by/3.0/deed.en_US
[Elixir Style Guide]: https://github.com/levionessa/elixir_style_guide
