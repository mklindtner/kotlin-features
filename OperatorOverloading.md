#Kotlin infix/suffix Operators

##Introduction
operator overloading is when we decide to override common expressions (+, -, !) 
in practice this is rarely done, but can be very useful especially within domain specific languages.
We can percieve it as extending the capabilities of kotlin. 


##Operators General
has fixed symbolic presence and precedence remains fixed.
To overload an operator we must use a member or extension function.


##Unary operators

###data class/member function
``` class Square(val length: Int, val width: Int) {
    var area = length * width

    //member function
    operator fun plus(other: Square ): Square {
        return Square(other.length + this.length, this.width + other.width)
    }
}

```

unary is operators who performs a single operation.
unaryMinus()
```operator fun Square.unaryMinus() = Square(-length, -width)

```
//example here

unaryPlus()
//example here

notOperator()
//how to do it wrong
```operator fun Square.not() = Square((length*length) * -1, (width*width) * -1)

```

##Binary operators
Binary operators uses two values, thus they are named binary.
When implementing these operators a parameter is required.
These are often useful for quickly defining simple behavior and avoiding cermonial code

Examples of typical operators





multiply
```operator fun Square.times(b: Int) = Square(length *b, width*b)

```


in operator (compareTo)
```operator fun Square.contains(a: Int) = Square(length, width).area <= a


```



##infix operators
infix operators has lower precedence than arithmetic operators, type casts and the rangeTo operator
```infix fun Square.neg(x: Int): Int {
    return -(this.length) + x
}

infix fun Int.Square(x: Square): Int {
    return this + x.length + x.width
}
```

