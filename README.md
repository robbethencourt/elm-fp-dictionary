# elm-fp-dictionary
I've been learning functional programming, but don't know what all the terminology means. I'm using this repo to define functional programming terms with examples in elm so I can quickly reference them. Some of the definitions may be incomplete as this is a learning tool.

### Table of Contents
* [Currying](#currying)
* [Partial Application](#partial-application)
* [Higher Order Function](#higher-order-function)
* [Lambda](#lambda)
* [Pattern Matching](#pattern-matching)
* [Recursion](#recursion)
* [Functor](#functor)
* [Pointed Functor](#pointed-functor)
* [Monoid](#monoid)

---

### Currying
A curried function is one that has been converted from a function that requires taking all arguments at once, to a function that is capable of taking one argument at a time, and returning a function capable of taking the remaining arguments. In elm all functions are curried! This means that all functions can be [partially applied](#partial-application)

*example*

```elm
-- all functions are curried in elm! Hooray, no extra work!
```

---

### Partial Application
Partial application can be used when a function allows one argument to be passed at a time. In elm, all functions can be partially applied as all functions are curried. When less than all the arguments are applied to a function, that function returns a new function that takes the remaining arguments.

*example*
```elm
add : Int -> Int -> Int
add a b = a + b
add5 = add 5
add5 10 -- 15
```

---

### Higher Order function
A higher order functions is one that takes a function as an argument. A function is also considered a higher order function if it returns a function.
##### Return Function from Function example

*example*
```elm
-- see add5 = add 5 in Partial Application example above
```

##### Function as Argument Example
*example*
```elm
type alias User =
    { firstName : String
    , lastName : String
    , unreadMessages : Int
    }

user : User
user = { firstName = "Rob", lastName = "Bethencourt", unreadMessages = 3 }


hello : User -> String
hello user =
    "Hello, " ++ user.firstName

notifyUnredMessages : (User -> String) -> User -> String
notifyUnredMessages greeting user =
    greeting user
    ++ ". You have "
    ++ (toString user.unreadMessages)
    ++ " unread messages."
```

---

### Lambda
A lambda is an anonymous function. (a function without a name) These functions are usually passed as arguments to [higher order functions](#higher-order-function). We can see these used most often as the first argument to the List.map and List.filter functions in elm.

In elm a lambda starts with a backslash \ and then you list the arguments the lambda takes. The right side of the arrow -> is where you write the function body.

```elm
myArray : List Int
myArray = List.range 1 5

List.map (\a -> a + 1) myArray

List.filter (\a -> a > 2) myArray
```

---

### Pattern Matching
Pattern matching allows us to check the pattern of a value and return something based on the pattern that is matched. Below are two simple examples we can build upon when we use [pattern matching](#pattern-matching) with [recursion](#recursion).
##### Int Example
Say we have a discount to apply if a user orders more than 2 items. We can pattern match off the number of items and return True or False based how many items they are purchasing. The _ is considered a wildcard, which matches all other cases and returns that particular value.

*example*
```elm
applyDiscount : List Item -> Bool
applyDiscount items =
    case List.length items of
        0 ->
            False

        1 ->
            False

        2 ->
            False

        _ ->
            True
```
##### List Example
We can also pattern match off of the structure of a list. We can match on an empty list, a list with one element, or a list with more than one element. Let's say we would like to display a message based on the number of products returned by a search.

*example*

```elm
lowCountMessage : List Item -> String
lowCountMessage items =
    case items of
        [] ->
            "No items found. Try searching again with different key words."

        [x] ->
            "Only 1 item found. Try expanding your search criteria."

        h :: rest ->
            ""
```

The [] represents an empty list.
the [x] represents only one item in the list.
The h :: rest syntax represents a list with two or more items. The h represents the first element in the list, and rest represents a List containing the remaining values of the list.

Also, We could have used the wildcard _ symbol instead of h :: rest in this scenario, but this gives us a look at the syntax for using [pattern matching](#pattern-matching) with [recursion](#recursion).

---

### Recursion

Recursion is when a function calls itself. In elm we can use pattern matching to continue calling the function, or match a pattern to return a final result. We can recreate javascript's reduce with pattern matching and recursion to return the total of a list of integers.

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

Because the last thing our reduceEx function does is call itself, this function is considered to have proper tail call optimization, or tail call elimination. When we write a recursive function this way in elm, it creates a while loop in our javaScript code to optimize for performance.

---

### Functor

A functor is anything that implements the map function while preserving identity and is composable. This means that a List in elm is a functor. The map function can be applied to it, it returns the same value if we use a lambda of (\x -> x) as the function we pass to it, and it's composable.

##### Preserving identity
This basically means if map is called, it is capable of returning itself.

_**note:** I'm using List.range to create a list of integers from 1 to 5. The identity function I'm using is provided by Elm, and it returns whatever value is passed to it._

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
type alias User =
    { firstName : String
    , lastName : String
    }

users : List User
users = [{firstName = "Rob", lastName = "Bethencourt"}, {firstName = "John", lastName = "Doe"}]

fullName : User -> String
fullName user =
    user.firstName ++ " " ++ user.lastName

sayItLoud : String -> String
sayItLoud a =
    String.toUpper a

loudFullName : User -> String
loudFullName user =
    sayItLoud (fullName user)

combineUsers : List User -> List String
combineUsers users =
    users
        |> List.map fullName
        |> List.map sayItLoud


-- or we can write it like this

combineUsersWithCompose : List User -> List String
combineUsersWithCompose users =
    users
        |> List.map (\user -> (sayItLoud (fullName user)))
```

---

### Pointed Functor
A pointed functor is a functor with an of method. This means we can take a value, and wrap it in a functor. In elm, lists have the singleton function that takes a value and wraps it in a List, which in elm is a functor.

*I'm still a little unclear if this is the entire story behind pointed functors.*

The value of a pointed functor seems to be that because this value is now wrapped in a functor, it allows you to map over the value returned.

*example*
```elm
List.singleton 1 -- [1]
List.singleton "hello" -- ["hello"]
List.singleton {firstName = "Rob", lastName = "Bethencourt"} -- [{firstName = "Rob", lastName = "Bethencourt"}]
```

---

### Monoid
A monoid has two characteristics. It needs to be able to preserve identity and be associative. Associativity is not to be confused with inverse, which is not a characteristic of a monoid.

##### Preserving Identity
Lists, Ints and Strings in elm hold to this characteristic.

*example*

```elm
List.append [] ([1, 2, 3]) -- [1, 2, 3]
77 + 0 -- 77
85 * 1 -- 85
String.append "" "I'm a monoid" -- "I'm a monoid"
```

##### Associativity
Lists, Ints and Strings also adhere to associativity. Meaning the grouping of operations doesn't affect the result.

*example*

```elm
List.append [1, 2, 3] (List.append [4, 5, 6] [7, 8, 9]) === List.append (List.append [1, 2, 3] [4, 5, 6]) [7, 8, 9]
1 + (2 + 3) == (1 + 2) + 3
1 * (2 * 3) == (1 * 2) * 3
"I'm " ++ ("a " ++ "monoid") == ("I'm " ++ "a ") ++ "monoid"
```

*there's more going on here. I need to find out how knowing these characteristic can be used in meaningful ways*
