# Kotlin infix/suffix Operators

## Introduction
operator overloading is when we decide to override common expressions (+, -, !, *) 
in practice this is rarely done, but can be very useful especially within domain specific languages.
We can percieve it as extending the capabilities of a specific kotlin class. 


## Operators General
has fixed symbolic presence and precedence remains fixed.
To overload an operator we must use a member or extension function.


### Extension Function
Are resolved statically for a class afterwards. Meaning that behavior is not added to the class but simply added dot-notation to the class, this means that a reference will not influence the original behavior should we choose to override. 
//show example
``` Kotlin
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D())
```


### member function
normally added behavior to the class.


### data class we will derive examples from
```kotlin
class Square(val length: Int, val width: Int) {
    var area = length * width

    unaryPlus()
    //member function    
    operator fun plus(other: Square ): Square {
        return Square(other.length + this.length, this.width + other.width)
    }
}

```

## Unary operators
unary is operators who performs a single operation they can be used to define singular behavior within a class.

### notOperator()
how NOT to do it (huehue)
```kotlin 
operator fun Square.not() = Square((length*length) * -1, (width*width) * -1)

```

### unaryMinus()
example: we want to draw a square on a 2D-graph and need to the opposite coordinates (or just black holes ... )
```Kotlin
operator fun Square.unaryMinus() = Square(-length, -width)

```



## Binary operators
Binary operators uses two values, one formal parameter and  thus they are named binary.
When implementing these operators a parameter is required.
These are often useful for quickly defining simple behavior and avoiding cermonial code

Examples of typical operators



### multiply
```Kotlin 
operator fun Square.times(b: Int) = Square(length *b, width*b)

```


### in operator (compareTo)
```Kotlin
operator fun Square.contains(a: Int) = Square(length, width).area <= a


```



## infix operators
infix operators has lower precedence than arithmetic operators, type casts and the rangeTo operator
```Kotlin
infix fun Square.neg(x: Int): Int {
    return -(this.length) + x
}

infix fun Int.Square(x: Square): Int {
    return this + x.length + x.width
}
```

