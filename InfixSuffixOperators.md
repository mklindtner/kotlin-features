import java.lang.Thread.sleep
import kotlin.concurrent.thread

fun main(args: Array<String>) {
    val square = Square(5, 10)
    val square2 = Square(5, 10)
    println(-square) //Square(-5,-10)
    println(!square) //Square(-25, -100)
    println(square.times(2)) //Square(10, 20)
    println(square.contains(25)) // false
    println(square.plus(square2)) //Square(10, 20)
    println(square.neg(3)) //-2
    println(10.Square(square)) //25 
}


//how to use -> Squares might need to have negative sides if we want to draw it in a coordinate system
data class Square(val length: Int, val width: Int) {
    var area = length * width

    //member function
    operator fun plus(other: Square ): Square {
        return Square(other.length + this.length, this.width + other.width)
    }
}

//extension function examples
operator fun Square.unaryMinus() = Square(-length, -width)
//how not to use
operator fun Square.not() = Square((length*length) * -1, (width*width) * -1)
//a very boring and correct example
operator fun Square.times(b: Int) = Square(length *b, width*b)
//a dystopian view of a Square
operator fun Square.contains(a: Int) = Square(length, width).area <= a

//infix example
infix fun Square.neg(x: Int): Int {
    return -(this.length) + x
}

infix fun Int.Square(x: Square): Int {
    return this + x.length + x.width
}








