# Java 8
## Interfaces

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