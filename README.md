# discriminated-union
Create an ["algebraic data type"](https://en.wikipedia.org/wiki/Algebraic_data_type) in pure PHP. There are many advantages to using ADT's, but most of them are moot in [weak](https://en.wikipedia.org/wiki/Strong_and_weak_typing#Definitions_of_%22strong%22_or_%22weak%22) languages like PHP. Furthermore, there are **many** composer packages that implement this better than I have here - it is mostly an educational project.

> **NOTE:** PHP 8.0 introduced the `match` keyword, meaning this Package will no longer work on PHP versions above 7.4

**Make an Option Type**
```php
$Option = Union('Option')
            ->of('Success', fn($item)  => item)
            ->of('Error',   fn($error) => $error)
            ->render();

$failureExample = $Option->Error('Nothing worked right!');
$successExample = $Option->Success('Something went right!');

return match($successExample)->with(
  'Success' => fn($successObject) => $successObject,
  'Error'   => fn($errorObject)   => die()
)
```

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
# License
This program can be licensed under the MIT license.
