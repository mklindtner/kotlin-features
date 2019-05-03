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
example
``` Kotlin
open class C

class D: C()

fun C.foo() = "c"

fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D()) //prints c
```


### member function
normally added behavior to the class. These take priority over extension functions


### data class we will derive examples from
```kotlin
class Vektor(val length: Int, val width: Int) {
    var area = length * width

    unaryPlus()
    //member function    
    operator fun plus(other: Vektor ): Vektor {
        return Vektor(other.length + this.length, this.width + other.width)
    }
}

```

## Unary operators
unary is operators who performs a single operation they can be used to define singular behavior within a class.

### notOperator()
how NOT to do it (huehue)
```kotlin 
operator fun Vektor.not() = Vektor((length*length) * -1, (width*width) * -1)

```

### unaryMinus()
example: we want to draw a vektor on a 2D-graph and need to the opposite coordinates (or just black holes ... )
```Kotlin
operator fun Vektor.unaryMinus() = Vektor(-length, -width)

```



## Binary operators
Binary operators uses two values, one formal parameter and  thus they are named binary.
When implementing these operators a parameter is required.
These are often useful for quickly defining simple behavior and avoiding cermonial code

Examples of typical operators



### multiply
```Kotlin 
operator fun Vektor.times(b: Int) = Vektor(length *b, width*b)

```


### in operator (compareTo)
```Kotlin
operator fun Vektor.contains(a: Int) = Vektor(length, width).area <= a


```



## infix operators
infix operators has lower precedence than arithmetic operators, type casts and the rangeTo operator
```Kotlin
infix fun Vektor.neg(x: Int): Int {
    return -(this.length) + x
}

infix fun Int.Vektor(x: Vektor): Int {
    return this + x.length + x.width
}
```

## Examples
``` Kotlin
fun main(args: Array<String>) {
    val vektor = Vektor(5, 10)
    val vektor2 = Vektor(5, 10)
    println(-vektor) //Vektor(-5,-10)
    println(!vektor) //Vektor(-25, -100)
    println(vektor.times(2)) //Vektor(10, 20)
    println(vektor.contains(25)) // false
    println(vektor.plus(vektor2)) //Vektor(10, 20)
    println(vektor.neg(3)) //-2
    println(10.Vektor(vektor)) //25 
}
```
