# elm-fp-dictionary
I've been learning functional programming, but don't know what all the terminology means that I read about and hear on podcasts. I'm using this repo to define functional programming terms with examples in elm so I can quickly reference them.

### Table of Contents
* [Currying](#currying)
* [Partial Application](#partial-application)
* [Pattern Matching](#pattern-matching)
* [Recursion](#recursion)
* [Functor](#functor)

### Currying
A curried function is one that has been converted from taking all arguments at once, to returning a function capable of taking the remaining arguments if not all are applied when the function is called. In elm all functions are curried! This means that all functions can be [partially applied](#partial-application)

*example*

```elm
-- all functions! Hooray!
```

### Partial Application
Partial application can be used when a function allows one argument to be passed at a time. In elm, all functions can be partially applied as all functions are curried. When less than all the arguments are applied to a function, that function returns a new function that takes the remaining arguments.

*example*
```elm
add : Int -> Int -> Int
add a b = a + b
add5 = add 5
add5 10 -- 15
```

### Pattern Matching
Pattern matching allows you to check the pattern of a value and return something based on the pattern that is matched.
##### Int Example
Say we have a discount to apply if a user orders more than 2 items. We can pattern match off the number and return True or False based how many items they are purchasing. The _ is considered a wildcard, which matches all other cases and returns that particular value.

*example*
```elm
applyDiscount : Int -> Bool
applyDiscount a =
    case a of
        1 ->
            False

        2 ->
            False

        _ ->
            True
```
##### List Example
We can also pattern match off of lists. We can match on an empty list, with one element, or more than one element. Let's say a user  searches for a certain item in our database, and we want to display a message if there were no items left in the inventory so a message encourages them to check back when the stock is replenished, a warning that there is only one item left to encourage them to order soon, or no message if 2 or more items are available.

*example*

```elm
lowVolumeMessage : List Item -> String
lowVolumeMessage items =
    case items of
        [] ->
            "No items available. Check back soon."

        [x] ->
            "Only 1 item left! Order soon."

        h :: rest ->
            ""
```
The h :: rest syntax is destructuring the list into a value for the first element in the list, and a List containing the remaining values of the list.

Also, We could have used the wildcard _ symbol instead of h :: rest in this scenario, but this gives us a look at the syntax for using [pattern matching](#pattern-matching) with [recursion](#recursion).

### Recursion

Recursion is when a function calls itself. In elm we can use pattern matching to continue calling the function, or match a pattern to return a final result. We can recreate javascript's reduce with pattern matching and recursion.

*example*

```elm
reduceEx : Int -> List Int -> Int
reduceEx val list =
   case list of
       [] ->
           val

       h :: rest ->
           reduceEx (h + val) rest
```

Because the last thing our reduceEx function does is call itself, this function is considered to have proper tail call optimization, or tail call elimination. When we write a recursive function this way in elm, it creates a while loop in our javaScript code to optimize performance.

### Functor

A functor is anything that implements the map function while preserving identity and is composable. This means that a List in elm is a functor. The map function can be applied to it, it returns the same value if we use a lambda of (\x -> x) as the function we pass to it, and it's composable

##### Preserving identity
This basically means if map is called, it is capable of returning itself.

_**note:** I'm using List.range to create a list of integers from 1 to 5. The identity function I'm using is provided by Elm, and it  returns whatever value is passed to it._

*example*

```elm
myArray : List Int
myArray = List.range 1 5

List.map identity myArray -- [1, 2, 3, 4, 5]
```

##### Is Composable
A functor also needs to be composable, meaning we can combine functions that would be used as the first argument to the map function into one function. Elm gives us the ability to compose functions out of the box, so this is totally doable.

*example*

```elm
users : List User
users [{firstName = "Rob", lastName = "Bethencourt"}, {firstName = "John", lastName = "Doe"}]

fullName : User -> String
fullName user =
    user.firstName ++ " " ++ user.lastName

sayItLoud : String -> String
sayItLoud a ->
    String.toUpper a

loudFullName User -> String
loudFullName user =
    sayItLoud (fullName user)

users
    |> List.map fullName
    |> List.map sayItLoud


-- or we can write it like this


users
    |> List.map loudFullName
```
