# discriminated-union
This package  provides a new approach to complex, composite types for PHP. Here's an example of how easy it is to make an "Result"
type (one of the most beloved ADTs around):

**Make a Result Type**
```php
$Result = Union('Result')
            ->of('Success', fn($item)  => item)
            ->of('Error',   fn($error) => $error)
            ->render();

$failureExample = $Result->Error('Nothing worked right!');
$successExample = $Result->Success('Something went right!');

return match($successExample)->with(
  'Success' => fn($successObject) => $successObject,
  'Error'   => fn($errorObject)   => die()
)
```
(Do note that currently match is not supported - this README is being created within 2 hours of the project's inception)

# So, what's going on here?
Algebraic data types are **composite data types**. They are sometimes called "Tagged Unions", "Discriminated Unions", or sometimes
even just "enums". It's almost exactly the same as having three classes: an abstract superclass, and two children which cannot
inherit one-another.

Think of a **Pet** class. A Pet can either be a dog or a cat.

`$Pet = Union('Pet')->of('Dog')->of('Cat')->render();`

You can own a "Dog", or you can own a "Cat". But under no circumstance can you own "just" a "Pet". It has to be one of those
two.

**Instantiate a new dog**  
`$dog = $Pet->Dog`

Now, imagine if we had fields such as "MeowCount" or "BarkCount". Only cats have meows and only dogs have barks. So you
can use a **Subtype Constructor** to represent that:

```php
$Pet = Union('Pet')
        ->of('Dog', fn($barkCount) => $barkCount)
        ->of('Cat', fn($meowCount) => $meowCount)
```

Now your functions can accept a `Pet` object.

`function countNoises(/*$Pet*/ $pet) {`

And you can **exhaustively match** all "arms" of your pet.

```php
return match($pet)->with(
  'Dog'   => fn($barkCount) => "Dog barked $barkCount times",
  'Error' => fn($meowCount) => "Cat meowed $meowCount times"
);
```

**Caveat Emptor**  
...This is cool, but it's also PHP we're talking about here. The main benefit of discriminated unions (provable exhaustive matching)
is completely lost on PHP's type system. But hopefully the few runtime checks that this match function provides at least
make it safer than "naked" PHP code.

# How does it work?
The actual "core" of how this works is actually implemented in about 50 lines of code, which is currently all packed into `Union.php`.

The code can explain it better. Basically, your arrow functions act as constructors for this dynamic thunk-constructor tree thing.

# License
This program can be licensed under the MIT license.
