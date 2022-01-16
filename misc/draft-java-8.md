# Java 8
## Interfaces part 1

### Default methods
**Before Java 8:** Interfaces can have only abstract methods.
**Why?**  
When a new method is added to an interface, breakage occurs in all implementing classes since they wouldn't have the implementation. This change introduces backward compatibility.  

**After Java 8:** New methods in interfaces can be added as default methods, with a default implementation.  
Implementing classes can override or use it as necessary.  

However, this change introduces **the diamond problem**.  

Example:  
```java
//This will not compile!
public class RomanNumeral implements Number, Alphabet {
    @Override
    public boolean isValid(String value) {
        throw new UnsupportedOperationException();
    }

    public static void main() {
        RomanNumeral numeral = new RomanNumeral();
        numeral.printDefaultValue();
    }
}
...
public interface Number {
    boolean isValid(String value);

    default void printDefaultValue() {
        System.out.println("Default value is the number -1");
    }
}
...
public interface Alphabet {
    boolean isValid(String value);

    default void printDefaultValue() {
        System.out.println("Default value is the alphabet A");
    }
}
```

When `numeral.printDefaultValue()` is called, which implementation will be called - `Number`'s or `Alphabet`'s?  

When there's a potential diamond problem, compilation will fail.  
To resolve this, the common method needs to be either overriden:  

```java
//This compiles
public class RomanNumeral implements Number, Alphabet {
    ...
    @Override
    public void printDefaultValue() {
        //Add custom implementation
        System.out.println("Default value is the Roman Numeral XII");

        //Or call default implementations
        Number.super.printDefaultValue();
        Alphabet.super.printDefaultValue();
    }

    public static void main() {
        RomanNumeral numeral = new RomanNumeral();
        numeral.printDefaultValue();
    }
}
```

### Static methods
Similar to default methods, but:  
- **Cannot be overridden**  
- **Cannot be called from object of implementing class**

Example:
```java
public class RomanNumeral implements Number {
    /*
     * //Will not compile if this is added - Static methods can't be overridden
     * @Override
     * public void printDefaultValue() {
     *     System.out.println("Default value is the Roman Numeral XII");
     * }
     */

    public static void main() {
        RomanNumeral numeral = new RomanNumeral();
        
        //Will not compile - Static methods can't be called from implementations's object
        //numeral.printDefaultValue();

        //This works
        Number.printDefaultValue();
    }
}
...
public interface Number {
    static void printDefaultValue() {
        System.out.println("Default value is the number -1");
    }
}
```

### Functional Interfaces
An interface that has only one abstract method. It can contain multiple other default methods; this is used in Lambda expressions.  
The `java.util.function` package contains many new Functional interfaces such as `Consumer`, `Supplier`, etc

A functional interface can be annotated with the `@FunctionalInterface` annotation - this prevents accidental addition of more abstract methods, and throws a compilation error.

Example:  
```java
package java.util.function;

@FunctionalInterface
public interface Consumer<T> {
    void accept(T t); //The only abstract method

    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

## Lambda Functions
This is an attempt to introduce functional programming in Java. In functional programming, functions can be passed as parameters; traditionally, functions have existed only within the scope of objects.  
A lambda expression is an anonymous function - ie., it doesn't have an identifier.  

### Building a Lambda function
Consider a `PriorityQueue` which is a min-heap by default, and needs to be changed into a max-heap:  
```java
public class Main
{
    public static void main(String[] args) {
        PriorityQueue<Integer> nums = new PriorityQueue<>(new CustomComparator());
    }
}

//Reverse the order of comparison from a, b to b, a
class CustomComparator implements Comparator<Integer> {
    @Override
    public int compare(Integer a, Integer b) {
        return Integer.compare(b, a);
    }
}
```

Can be simplified into:
```java
public class Main
{
    public static void main(String[] args) {
        PriorityQueue<Integer> nums = new PriorityQueue<>(new Comparator<Integer>() {
            @Override
            public int compare(Integer a, Integer b) {
                return Integer.compare(b, a);
            }
        });
    }
}
```

We don't need to specifically mention `new Comparator`; it will be handled by the compiler.
```java
    PriorityQueue<Integer> nums = new PriorityQueue<>(
        public int compare(Integer a, Integer b) {
            return Integer.compare(b, a);
        }
    );
```

We know that the `Comparator` interface has only one method, and so don't need to provide the method name either.
```java
    PriorityQueue<Integer> nums = new PriorityQueue<>(
        public int (Integer a, Integer b) {
            return Integer.compare(b, a);
        }
    );
```

The only method in the interface takes 2 `int` parameters and returns and `int`; so mentioning it again is redundant.  
```java
    PriorityQueue<Integer> nums = new PriorityQueue<>(
        (a, b) -> {
            Integer.compare(b, a);
        }
    );
```

Now since there's only one line in the body, `{}` is also unnecessary:  
```java
    PriorityQueue<Integer> nums = new PriorityQueue<>((a, b) -> Integer.compare(b, a));
```

## Streams API
[Streams JavaDoc](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)  
A stream is a sequence of objects that supports various methods which can be pipelined to produce the desired result.  
- A Stream isn't a data structure, but takes inputs from collections, arrays or I/O channels.
- Streams don’t change the original data structure.
- Streams are lazy. Computation on the source data is only performed when the terminal operation is initiated, and source elements are consumed only as needed.
- Terminal operations mark the end of the stream and return the result.

**Collections vs streams**: Collections and streams, while bearing some superficial similarities, have different goals.  
Collections are primarily concerned with the efficient management of, and access to, their elements. By contrast, streams do not provide a means to directly access or manipulate their elements and are instead concerned with declaratively describing their source and the computational operations which will be performed in aggregate on that source.

### Creating Streams
```java
Stream<String> stream;

//Empty stream
stream = Stream.empty();

//Directly
stream = Stream.of("Nile");
stream = Stream.of("Nile", "Amazon", "Beas");

//Builder pattern
stream = Stream.<String>builder()
        .add("Nile")
        .add("Amazon")
        .add("Indus")
        .build();

//From arrays
String[] rivers = new String[]{"Nile", "Satluj"};
stream = Stream.of(rivers);
stream = Arrays.stream(rivers);

//From collections
List<String> list = Arrays.asList(rivers);
stream = list.stream();

//Generate an infinite sequential unordered stream
stream = Stream.generate(() -> UUID.randomUUID()); //stream of random UUIDs
stream = Stream.generate(UUID::randomUUID); //stream of random UUIDs, same as above
stream = Stream.generate(() -> 0); //stream of 0s
//For practical purposes, a limit needs to be specified.
stream = Stream.generate(() -> 0).limit(10); //stream of 10 0s

//Generate an infinite sequential ordered stream
//First param is the seed value, second param is the function. So stream is: seed, f(seed), f(f(seed)), ...
//Equivalent to an infinite for loop: for (int i = 1; ; i++)
stream = Stream.iterate(1, i -> i + 1); //1, 2, 3, ...
stream = Stream.iterate(1, i -> i * 2).limit(10); //Limit needs to be used


//Generate a finite sequential ordered stream
//Equivalent to a for loop: for (int i = 1; i < 10; i++)
stream = Stream.iterate(0, i -> i < 10, i -> i + 1);
```

### Stream operations
Types of operations:  
- **Intermediate:** Do not produce results, but manipulate and return a stream, like `filter()`  
- **Terminal:** Produce a result, like `collect()`



## Interfaces part 2
### Predicate functional interface
A predicate is a function that evaluates the given input and returns `true` or `false`.  
This interface has one abstract method `boolean test(T t)` and other default methods such as `and(Predicate<? super T> x)`, `or(Predicate<? super T> x)`, `isEqual(Object x)`, and `negate()`.  
[Predicate JavaDocs](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html)  

Example:  
```java
Predicate<Integer> isMajorPredicate = age -> age > 18;
Predicate<Integer> isSeniorCitizenPredicate = age -> age < 60;

boolean isEligibleToVote = isMajorPredicate.test(60);
boolean isEligibleToWork = isMajorPredicate.and(isSeniorCitizenPredicate);
```