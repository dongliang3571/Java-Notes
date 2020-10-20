# Java-Notes
Some Java Notes

### Just-In-Time(JIT) Overview

The Just-In-Time (JIT) compiler is a component of the Java™ Runtime Environment that improves the performance of Java applications at run time.

Java programs consists of classes, which contain platform-neutral bytecodes that can be interpreted by a JVM on many different computer architectures. At run time, the JVM loads the class files, determines the semantics of each individual bytecode, and performs the appropriate computation. The additional processor and memory usage during interpretation means that a Java application performs more slowly than a native application. The JIT compiler helps improve the performance of Java programs by compiling bytecodes into native machine code at run time.

The JIT compiler is enabled by default, and is activated when a Java method is called. The JIT compiler compiles the bytecodes of that method into native machine code, compiling it "just in time" to run. When a method has been compiled, the JVM calls the compiled code of that method directly instead of interpreting it. Theoretically, if compilation did not require processor time and memory usage, compiling every method could allow the speed of the Java program to approach that of a native application.

JIT compilation does require processor time and memory usage. When the JVM first starts up, thousands of methods are called. Compiling all of these methods can significantly affect startup time, even if the program eventually achieves very good peak performance.

In practice, methods are not compiled the first time they are called. For each method, the JVM maintains a call count, which is incremented every time the method is called. The JVM interprets a method until its call count exceeds a JIT compilation threshold. Therefore, often-used methods are compiled soon after the JVM has started, and less-used methods are compiled much later, or not at all. The JIT compilation threshold helps the JVM start quickly and still have improved performance. The threshold has been carefully selected to obtain an optimal balance between startup times and long term performance.

After a method is compiled, its call count is reset to zero and subsequent calls to the method continue to increment its count. When the call count of a method reaches a JIT recompilation threshold, the JIT compiler compiles it a second time, applying a larger selection of optimizations than on the previous compilation. This process is repeated until the maximum optimization level is reached. The busiest methods of a Java program are always optimized most aggressively, maximizing the performance benefits of using the JIT compiler. The JIT compiler can also measure operational data at run time, and use that data to improve the quality of further recompilations.

The JIT compiler can be disabled, in which case the entire Java program will be interpreted. Disabling the JIT compiler is not recommended except to diagnose or work around JIT compilation problems.

### List

Importing library: `import java.util.List`.

It's an interface. `ArrayList`, `LinkedList` implement it.


### ArrayList

Importing library: `import java.util.ArrayList`

Implements `List`

### LinkedList

Importing library: `import java.util.LinkedList`

Implements `List`

### LinkedList VS. ArrayList

| LinkedList                               | ArrayList                          |
| -----------------------------------      | ---------------------------------- |
| Implemented with doubly linked list      | Implemented with array             |
| get(i) costs O(n)                        | Get(i) costs O(1)                  |
| remove() costs O(1)                      | remove() costs O(n)                |
| add() costs O(1)                         | add() costs O(n)                   |
| Implement List interface                 | same                               |
| Maintain elments' orders after insertion | same                               |
| Non-synchronized. Use Collections.synchronizedList instead | same             |
| Fail-fast                                | same                               |


### Iteration for `Map` and `Set`

- for `Map`

```java
// old syntax
public static void printMap(Map mp) {
    Iterator it = mp.entrySet().iterator();
    while (it.hasNext()) {
        Map.Entry pair = (Map.Entry)it.next();
        System.out.println(pair.getKey() + " = " + pair.getValue());
        it.remove(); // avoids a ConcurrentModificationException
    }
}

// output:
// 1 = 1
// 2 = 2
// 3 = 3


// new syntax
for(Map.Entry<Integer, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// output:
// 1 = 1
// 2 = 2
// 3 = 3
```

- for 'Set'

```java
// old syntax
HashSet<Integer> sets = new HashSet<Integer>();
sets.add(1);
sets.add(2);

Iterator iter = sets.iterator();
while (iter.hasNext()) {
    System.out.println(iter.next());
}

// output:
// 1
// 2

//new syntax
HashSet<Integer> sets = new HashSet<Integer>();
sets.add(1);
sets.add(2);

for(Integer i : sets) {
    System.out.println(i);
}

// output:
// 1
// 2
```

### Map and Set Hashing

```java
HashSet<String> set = new HashSet<String>();
System.out.println(set.add(new String("123"))); // true
System.out.println(set.add(new String("123"))); // false
System.out.println(set.add("123")); // false

HashSet<Integer> set = new HashSet<Integer>();
System.out.println(set.add(new Integer(11))); // true
System.out.println(set.add(new Integer(11))); // false
System.out.println(set.add(11)); // false


// custom object without overrding equals and hashCode
class Tweet {
    public int userId;
    public int tweetId;

    public Tweet(int userId, int tweetId) {
        this.userId = userId;
        this.tweetId = tweetId;
    }
}

HashSet<Tweet> set = new HashSet<Tweet>();
System.out.println(set.add(new Tweet(1, 2))); // true
System.out.println(set.add(new Tweet(1, 2))); // true, because it uses Object's equals and hashCode

// Solution is to override equals and hashCode
https://dzone.com/articles/things-to-keep-in-mind-while-using-custom-classes

```

### Exception handling

https://docs.oracle.com/javase/tutorial/essential/exceptions/index.html

**Checked Exception VS. Unchecked Exception**

**Checked Exception:** are the exceptions that are checked at compile time. If some code within a method throws a checked exception, then the method must either handle the exception or it must specify the exception using throws keyword.

For example, consider the following Java program that opens file at locatiobn “C:\test\a.txt” and prints first three lines of it. The program doesn’t compile, because the function main() uses FileReader() and FileReader() throws a checked exception FileNotFoundException. It also uses readLine() and close() methods, and these methods also throw checked exception IOException

```java
import java.io.*;

class Main {
    public static void main(String[] args) {
        FileReader file = new FileReader("C:\\test\\a.txt");
        BufferedReader fileInput = new BufferedReader(file);

        // Print first 3 lines of file "C:\test\a.txt"
        for (int counter = 0; counter < 3; counter++)
            System.out.println(fileInput.readLine());

        fileInput.close();
    }
}
```

output:

```
Exception in thread "main" java.lang.RuntimeException: Uncompilable source code -
unreported exception java.io.FileNotFoundException; must be caught or declared to be
thrown
	at Main.main(Main.java:5)
 ```

 To fix the above program, we either need to specify list of exceptions using throws, or we need to use try-catch block. We have used throws in the below program. Since FileNotFoundException is a subclass of IOException, we can just specify IOException in the throws list and make the above program compiler-error-free.

```java
import java.io.*;

class Main {
    public static void main(String[] args) throws IOException {
        FileReader file = new FileReader("C:\\test\\a.txt");
        BufferedReader fileInput = new BufferedReader(file);

        // Print first 3 lines of file "C:\test\a.txt"
        for (int counter = 0; counter < 3; counter++)
            System.out.println(fileInput.readLine());

        fileInput.close();
    }
}
```

**Unchecked Exception:** are the exceptions that are not checked at compiled time. In C++, all exceptions are unchecked, so it is not forced by the compiler to either handle or specify the exception. It is up to the programmers to be civilized, and specify or catch the exceptions. In Java exceptions under `Error` and `RuntimeException` classes are unchecked exceptions, everything else under throwable is checked.

```
                   +-----------+
		   | Throwable |
                   +-----------+
                    /         \
		   /           \
          +-------+          +-----------+
          | Error |          | Exception |
          +-------+          +-----------+
	   /  |  \           / |        \
         \________/	     \______/    \
			                 +------------------+
          unchecked	     checked	 | RuntimeException |
				         +------------------+
					   /   |    |      \
                    		          \_________________/

					       unchecked
```

Consider the following Java program. It compiles fine, but it throws ArithmeticException when run. The compiler allows it to compile, because ArithmeticException is an unchecked exception.

```java
class Main {
   public static void main(String args[]) {
      int x = 0;
      int y = 10;
      int z = y/x;
  }
}
```

Output:

```
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at Main.main(Main.java:5)
Java Result: 1
```

If a client can reasonably be expected to recover from an exception, make it a checked exception. If a client cannot do anything to recover from the exception, make it an unchecked exception

```java
// Note: This class will not compile yet.
import java.io.*;
import java.util.List;
import java.util.ArrayList;

public class ListOfNumbers {

    private List<Integer> list;
    private static final int SIZE = 10;

    public ListOfNumbers () {
        list = new ArrayList<Integer>(SIZE);
        for (int i = 0; i < SIZE; i++) {
            list.add(new Integer(i));
        }
    }

    public void writeList() {
	// The FileWriter constructor throws IOException, which is a checked exception and must be caught.
        PrintWriter out = new PrintWriter(new FileWriter("OutFile.txt"));

        for (int i = 0; i < SIZE; i++) {
            // The get(int) method throws IndexOutOfBoundsException, which is a unchecked exception
            out.println("Value at: " + i + " = " + list.get(i));
        }
        out.close();
    }
}
```

`new FileWriter("OutFile.txt")` is a call to a constructor. The constructor initializes an output stream on a file. If the file cannot be opened, the constructor throws an `IOException`. `list.get(i)` is a call to the `ArrayList` class's `get` method, which throws an `IndexOutOfBoundsException` if the value of its argument is too small (less than 0) or too large (more than the number of elements currently contained by the `ArrayList`).

The class do not compile because we don't handle the exceptions, so to handle them we do:

```java
import java.io.*;
import java.util.List;
import java.util.ArrayList;

public class ListOfNumbers {

    private List<Integer> list;
    private static final int SIZE = 10;

    public ListOfNumbers () {
        list = new ArrayList<Integer>(SIZE);
        for (int i = 0; i < SIZE; i++) {
            list.add(new Integer(i));
        }
    }

    public void writeList() {
        PrintWriter out = null;
        try {
            System.out.println("Entered try statement");
            out = new PrintWriter(new FileWriter("OutFile.txt"));
            for (int i = 0; i < SIZE; i++) {
                out.println("Value at: " + i + " = " + list.get(i));
            }
        } catch (IndexOutOfBoundsException e) {
            System.err.println("IndexOutOfBoundsException: " + e.getMessage());
        } catch (IOException e) {
            System.err.println("Caught IOException: " + e.getMessage());
        }

        // or Catching More Than One Type of Exception with One Exception Handler
        // catch (IndexOutOfBoundsException | IOException e) {
        //      logger.log(e)
        //      throw ex;
        // }

        finally {
            if (out != null) {
                System.out.println("Closing PrintWriter");
                out.close();
            } else {
                System.out.println("PrintWriter not open");
            }
        }
    }
}
```

Or we can Specifying the Exceptions Thrown by a Method

```java
public void writeList() throws IOException, IndexOutOfBoundsException {
	// ...code
}
```

Remember that `IndexOutOfBoundsException` is an unchecked exception; including it in the throws clause is not mandatory. You could just write the following.

```java
public void writeList() throws IOException {
	// ...code
}
```

### Why checked and unchecked exceptions

https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html

Checked exceptions extend java.lang.Exception, while unchecked exceptions extend java.lang.RuntimeException, or java.lang.Error.

Exception extends java.lang.Throwable, while RuntimeException extends Exception, and Error, like Exception, extends java.lang.Throwable.

When deciding whether you should be using a checked vs. unchecked exception, always remember these rules:

ExceptionS are cases an application would want to handle.
RuntimeExceptionS are cases you (usually) can't handle, due to programming error. You shouldn't catch RuntimeExceptionS, they should be targeted in your unit testing, and fixed in your production code.
ErrorS are cases you can't handle because of critical errors, such as system problems (e.g. file system has failed). You shouldn't ever throw, catch or subclass an Error, unless you're building something such as a compiler for the JVM.

### Override and Hiding Methods

### Instance Method

An instance method in a subclass with the same signature (name, plus the number and the type of its parameters) and return type as an instance method in the superclass overrides the superclass's method.

The ability of a subclass to override a method allows a class to inherit from a superclass whose behavior is "close enough" and then to modify behavior as needed. The overriding method has the same name, number and type of parameters, and return type as the method that it overrides. ***An overriding method can also return a subtype of the type returned by the overridden method. This subtype is called a covariant return type.***


### Static Method

If a subclass defines a static method with the same signature as a static method in the superclass, then the method in the subclass hides the one in the superclass.

**Note:** The distinction between hiding a static method and overriding an instance method has important implications:

- The version of the overridden instance method that gets invoked is the one in the subclass.
- The version of the hidden static method that gets invoked depends on whether it is invoked from the superclass or the subclass.


Consider an example that contains two classes. The first is Animal, which contains one instance method and one static method:

```java
public class Animal {
    public static void testClassMethod() {
        System.out.println("The static method in Animal");
    }
    public void testInstanceMethod() {
        System.out.println("The instance method in Animal");
    }
}
```

The second class, a subclass of Animal, is called Cat:

```java
public class Cat extends Animal {
    public static void testClassMethod() {
        System.out.println("The static method in Cat");
    }
    public void testInstanceMethod() {
        System.out.println("The instance method in Cat");
    }

    public static void main(String[] args) {
        Cat myCat = new Cat();
        Animal myAnimal = myCat;
        Animal.testClassMethod();
        myAnimal.testInstanceMethod();
    }
}
```

The Cat class overrides the instance method in Animal and hides the static method in Animal. The main method in this class creates an instance of Cat and invokes testClassMethod() on the class and testInstanceMethod() on the instance.

The output from this program is as follows:

```
The static method in Animal
The instance method in Cat
```

As promised, the version of the hidden static method that gets invoked is the one in the superclass, and the version of the overridden instance method that gets invoked is the one in the subclass.

### Inheritance


Assume we have class `Dog` and `ShowDog`, and `ShowDog` is inherited from `Dog`.

```java
public class Dog {
    public void bark() {
        System.out.println("Dog");
    }
    public void barkMany(int N) {
        for(int i = 0; i < N; i++) {
            bark();
        }
    }
}

public class ShowDog extends Dog {
    @override
    public void barkMany() {
        System.out.println("As a dog, I say: ");
        bark();
    }

    public static void main(String[] args) {
        ShowDog sd = new ShowDog();
        sd.barkMany(3);
        // output:
        // As a dog, I say:
        // Dog
        // Dog
        // Dog

        // will call overriding barkMany, and inside barkMany, bark() will be Dog's bark(), because ShowDog is
        // inheried from Dog.
    }
}
```

Now we change class `Dog` into:

```java
public class Dog {
    public void bark() {
        bakrMany(1);
    }
    public void barkMany(int N) {
        for(int i = 0; i < N; i++) {
            System.out.println("Dog");
        }
    }
}

public class ShowDog extends Dog {
    @override
    public void barkMany() {
        System.out.println("As a dog, I say: ");
        bark();
    }

    public static void main(String[] args) {
        ShowDog sd = new ShowDog();
        sd.barkMany(3);
        // output:
        // As a dog, I say:
        // As a dog, I say:
        // As a dog, I say:
        // As a dog, I say:
        // ........
        // error!

        // will call overriding barkMany, and inside barkMany, bark() will be Dog's bark(), but inside bark(),
        // ShowDog's barkMany() will be called. Because the runtime type for sd is ShowDog, not Dog.
    }
}
```

### Abstract Class

An abstract class is a class that is declared `abstract`—it may or may not include abstract methods. Abstract classes cannot be instantiated, but they can be subclassed.

If a class includes abstract methods, then the class itself must be declared abstract

```java
public abstract class GraphicObject {
   // declare fields
   // declare nonabstract methods
   abstract void draw();
}
```

One abstract class can be extended by another abstract class

```java
abstract class StaffMember {

}

abstract class Car extends StaffMember {

}
```

In Java, an abstract class can implement an interface, and **not provide implementations** of all of the interface’s methods.  It is the responsibility of the first concrete class that has that abstract class as an ancestor to implement all of the methods in the interface.

```java
interface MyInterface {
    public String hello = "Hello"; // By default it's final and static, e.g. public static final String hello = "Hello"
    public void sayHello();
}

abstract class StaffMember implements MyInterface {
    // No implementation of sayHello is fine
}

abstract class Car extends StaffMember {
    // No implementation of sayHello is fine
}
```

### Interface

In the Java programming language, an interface is a reference type, similar to a class, that can contain only constants, method signatures, default methods, static methods, and nested types. Method bodies exist only for default methods and static methods.

Interfaces **cannot** be instantiated—they can only be implemented by classes or extended by other interfaces

Varibales declared in Interface are always `static` and `final` because interface cannot be instantiated. It doesn't make sense to let them have memeber variables.

```java
interface MyInterface {
    public String hello = "Hello"; // By default it's final and static, e.g. public static final String hello = "Hello"
}

class Car implements MyInterface {

}

public class Solution {

    public static void main(String[] args) {
	System.out.println(MyInterface.hello); // print out Hello
	System.out.println(Car.hello); // print out Hello, Car gets all variable from MyInterface
        MyInterface.hello = "sdfdsf"; // Error, final variable cannot be changed
	Car.hello = "sdfdsf"; // Error, final variable cannot be changed
    }
}

```

### Abstract vs. Interface

1. Main difference is methods of a Java interface are implicitly abstract and cannot have implementations. A Java abstract class can have instance methods that implements a default behavior.
2. Variables declared in a Java interface is by default final. An  abstract class may contain non-final variables.
3. Members of a Java interface are public by default. A Java abstract class can have the usual flavors of class members like private, protected, etc..
4. Java interface should be implemented using keyword “implements”; A Java abstract class should be extended using keyword “extends”.
5. An interface can extend another Java interface only, an abstract class can extend another Java class and implement multiple Java interfaces.
6. A Java class can implement multiple interfaces but it can extend only one abstract class.
7. Interface is absolutely abstract and cannot be instantiated; A Java abstract class also cannot be instantiated, but can be invoked if a main() exists.
8. In comparison with java abstract classes, java interfaces are slow as it requires extra indirection.


### Access Modifiers

https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html#:~:text=The%20private%20modifier%20specifies%20that,its%20class%20in%20another%20package.

Access level modifiers determine whether other classes can use a particular field or invoke a particular method. There are two levels of access control:

- At the top level, like class — public, or package-private (no explicit modifier).
- At the member level, like member method — public, private, protected, or package-private (no explicit modifier).

A class may be declared with the modifier public, in which case that class is visible to all classes everywhere. If a class has no modifier (the default, also known as package-private), it is visible only within its own package (packages are named groups of related classes — you will learn about them in a later lesson.)

At the member level, you can also use the public modifier or no modifier (package-private) just as with top-level classes, and with the same meaning. For members, there are two additional access modifiers: private and protected. The private modifier specifies that the member can only be accessed in its own class. The protected modifier specifies that the member can only be accessed within its own package (as with package-private) and, in addition, by a subclass of its class in another package.

The following table shows the access to members permitted by each modifier.

| Modifier  | Class | Package | Subclass | World |
|-----------|-------|---------|----------|-------|
|  public   |   Y   |    Y    |     Y    |   Y   |
| protected |   Y   |    Y    |     Y    |   N   |
|no modifier|   Y   |    Y    |     N    |   N   |
|  private  |   Y   |    N    |     N    |   N   |


## Casting

### Compile-Time Type Checking

```java
public static void main(String[] args) {
    VengefulSList<Integer> vsl = new VengefulSList<Integer>(9);
    vsl.insertBack(50);

    SList<Integer> sl = vsl;

    sl.printLostItems();
    // Compilation errors! Because sl is SList in compile-Time, and SList
    // doesn't have method printLostItems().
}
```

- Compiler allows memory box to hold any subtype
- Compiler allows calls based on static type
- Overriden non-static methods are selected at runtime based on dynamic type(Check section of overriding and hiding method).

```java
interface Callee {
    public void print();
}

class Callee1 implements Callee {
    public void print() {
        System.out.println("Callee1");
    }
}

class Callee2 implements Callee {
    public void print() {
        System.out.println("Callee2");
    }
}

class Callee3 extends Callee2 {
    public void print() {
        System.out.println("Callee3");
    }
}

public class DogLauncher {
    public static void main(String[] args) {
        Object o2 = new Callee3();
        Callee3 c3 = (Callee3)o2;
        c3.print(); // output: Callee3

        Callee2 c2 = (Callee2)o2;
        c2.print(); // output: Callee3
        // even we already casted it to Callee2, but indeed it was instantiated with Callee3
        // so it will stay being Callee3 as runtime type forever in this program

        Object o3 = (Callee2)o2;
        o3.print();
	// Error in compile-time type checking: because Object has no method print()
    }
}

```

## Polymophism and overloading

Example of **dynamic method dispatch(or dynamic method selection)**

```java
interface Callee {
    public void print();
}

class Callee1 implements Callee {
    public void print() {
        System.out.println("Callee1");
    }
}

class Callee2 implements Callee {
    public void print() {
        System.out.println("Callee2");
    }
}

public class DogLauncher {
    public static void main(String[] args) {
        Callee callee1 = new Callee1();
        Callee callee2 = new Callee2();

        Callee[] arr = new Callee[2];
        arr[0] = callee1;
        arr[1] = callee2;

        for(Callee c : arr) {
            c.print();
            // output:
            // Callee1
            // Callee2
        }
    }
}
```

Note that in Java, dynamic method dispatch happens only for the object the method is called on(e.g. different subclass which inherited from same parent in an array type of the parent, calls same method), not for the parameter types of overloaded methods(example below).

```java
interface Callee {
    public void foo(Object o);
    public void foo(String s);
    public void foo(int i);
}

class CalleeImpl implements Callee {
    public void foo(Object o) {
        System.out.println("foo(Object o)");
    }

    public void foo(String s) {
        System.out.println("foo(\"" + s + "\")");
    }

    public void foo(int i) {
        System.out.println("foo(" + i + ")");
    }
}

public static void main(String[] args) {
    CalleeImpl callee = new CalleeImpl();
    String s = "haha";
    Object o = "haha";

    callee.foo(s); // output: foo("haha")
    callee.foo(o); // output: foo(Object o)

    // method was selected depending on compilation-type, not runtime-type
}
```


## Package

**Definition:** A package is a grouping of related types providing access protection and name space management. Note that types refers to classes, interfaces, enumerations, and annotation types. Enumerations and annotation types are special kinds of classes and interfaces, respectively, so types are often referred to in this lesson simply as classes and interfaces.

**Note:** Package are simply the directly path, i.e. `import tools.graphics.Draw` -> /tools/graphics/Draw.java
and `import tools.Dog` -> /tools/Dog.java.

**Naming convention**

- Package names are written in all lower case to avoid conflict with the names of classes or interfaces.

- Companies use their reversed Internet domain name to begin their package names—for example, com.example.mypackage for a package named mypackage created by a programmer at example.com. i.e /com/example/mypackage


**Directory:**

```
- tools
    ||
    | -->graphics
    |        |
    |         -->Draw.java
     --->Dog.java
- DogLauncher.java
```

`package` keyword has to be at the first line of the file

```java
// /tools/Dog.java

package tools;

public class Dog {
    public static void bark() {
        System.out.println("Dog");
    }
}
```

```java
// /tools/graphics/Draw.java

package tools.graphics;

public class Draw {
    public static void paint() {
        System.out.println("Draw");
    }
}
```

```java
// DogLauncher.java

import tools.Dog
import tools.graphics.Draw

public class DogLauncher {
    public static void main(String[] args) {
        Dog.bark(); // output: Dog
        Draw.paint();   // output: Draw
    }
}
```

Another way using package without using `import`, we can refer to a Package Member by Its Qualified Name

```java
// DogLauncher.java

public class DogLauncher {
    public static void main(String[] args) {
        tools.Dog.bark(); // output: Dog
        tools.graphics.Draw.paint();   // output: Draw
    }
}
```

### Apparent Hierarchies of Packages

At first, packages appear to be hierarchical, but they are not. For example, the Java API includes a `java.awt` package, a `java.awt.color` package, a `java.awt.font` package, and many others that begin with `java.awt`. However, the `java.awt.color` package, the `java.awt.font` package, and other `java.awt.xxxx` packages are not included in the `java.awt` package. The prefix `java.awt` (the Java Abstract Window Toolkit) is used for a number of related packages to make the relationship evident, but not to show inclusion.

Importing `java.awt.*` imports all of the types in the java.awt package, but it does not import `java.awt.color`, java.awt.font, or any other `java.awt.xxxx` packages. If you plan to use the classes and other types in `java.awt.color` as well as those in `java.awt`, you must import both packages with all their files:

```java
import java.awt.*;
import java.awt.color.*;
```

### The static import statment

There are situations where you need frequent access to static final fields (constants) and static methods from one or two classes. Prefixing the name of these classes over and over can result in cluttered code. ***The static import statement gives you a way to import the constants and static methods that you want to use so that you do not need to prefix the name of their class.***

The `java.lang.Math` class defines the `PI` constant and many static methods, including methods for calculating sines, cosines, tangents, square roots, maxima, minima, exponents, and many more. For example,

```java
public static final double PI = 3.141592653589793;
public static double cos(double a)
{
    ...
}

```

Ordinarily, to use these objects from another class, you prefix the class name, as follows.

```java
double r = Math.cos(Math.PI * theta);
```

You can use the static import statement to import the static members of java.lang.Math so that you don't need to prefix the class name, Math. The static members of Math can be imported either individually:

```java
import static java.lang.Math.PI;
```

or as a group:

```java
import static java.lang.Math.*;
```

Once they have been imported, the static members can be used without qualification. For example, the previous code snippet would become:

```java
double r = cos(PI * theta);
```

Obviously, you can write your own classes that contain constants and static methods that you use frequently, and then use the static import statement. For example,

```java
import static mypackage.MyConstants.*;
```


## Return Object

In Java, You always return a copy of reference (unless returned value is a primitive type such as int, float, char, ...).

So, if you don't want the returned object to be modified, you must return a full copy of it (you could use Clonable interface and clone method if your class defines it). Or the object is designed to be immutable.

## Bitwise operation

**Operators:**

With A = 60 = 0011 1100, B = 13 = 0000 1101,

| Operator | Description | Example |
|---|----|---|
| & (bitwise and) | Binary AND Operator copies a bit to the result if it exists in both operands. | (A & B) will give 12 which is 0000 1100 |
| | (bitwise or) | Binary OR Operator copies a bit if it exists in either operand.	| (A | B) will give 61 which is 0011 1101 |
| ^ (bitwise XOR) | Binary XOR Operator copies the bit if it is set in one operand but not both. | (A ^ B) will give 49 which is 0011 0001 |
| ~ (bitwise compliment) | Binary Ones Complement Operator is unary and has the effect of 'flipping' bits.	 | (~A ) will give -61 which is 1100 0011 in 2's complement form due to a signed binary number. |
| << (left shift) | Binary Left Shift Operator. The left operands value is moved left by the number of bits specified by the right operand.	 | A << 2 will give 240 which is 1111 0000
 |
| >> (right shift) | Binary Right Shift Operator. The left operands value is moved right by the number of bits specified by the right operand. | A >> 2 will give 15 which is 1111 |
| >>> (zero fill right shift) | Shift right zero fill operator. The left operands value is moved right by the number of bits specified by the right operand and shifted values are filled up with zeros.	 | A >>>2 will give 15 which is 0000 1111 |


**Note: It apply to all primitives, but with `Numeric Promotion`, it will promote things to at least `int` first**

```java
byte b = 7;
byte b = ~b;
// will give a error of 'error: incompatible types: possible lossy conversion from int to byte'
// because ~b is promoted to int
// correct way to do it is to cast b to byte again
// i.e.

byte b = (byte)~b;
```

### Java Web Development

[Introduction to Java Web Application Development](http://cse.csusb.edu/turner/java_web_programming/intro/)

### Java servlet

[Life of a JSP file](https://en.wikipedia.org/wiki/File:JSPLife.png#/media/File:JSPLife.png)

A Java servlet is a Java program that extends the capabilities of a server. Although servlets can respond to any types of requests, they most commonly implement applications hosted on Web servers.[1] Such Web servlets are the Java counterpart to other dynamic Web content technologies such as PHP and ASP.NET.

### Tomcat

http://www.codejava.net/servers/tomcat/how-to-deploy-a-java-web-application-on-tomcat#DeployMethod2

### Java EE Container > Java EE server > Web container > Servlet JSP , Enterprise JavaBeans (EJB) container > Enterprise Bean

[ Java EE application components in the Java EE containers](http://docs.oracle.com/javaee/5/tutorial/doc/bnabo.html#bnabr)

### Difference between a web server and a appplication server

**web server** - serves files stored somewhere (most commonly .css, .html, .js). Common web servers are Apache, Nginx or even Python's SimpleHTTPServer.

**application server** - serves files generated on the fly. Essentially most web servers have some sort of plugins or even come with built-in functionality to do that. There exist also strict application servers like Gunicorn (Python), Unicorn (Ruby), uWSGI (Python), etc

I think it should be said that **Tomcat(Web container)** has it's own HTTP server built into it, and is fully functional at serving static content too. Depending on your java virtual machine configuration it can actually outperform going through traditional connectors in apache such as mod_proxy and mod_jk.

That said a fully optimized Tomcat server should serve static files fast and if you have Java servlets, JSPs and ColdFusion files in addition to static content you may find tomcat does an excellent job by itself.

## Web Server, Nginx

**You can use Nginx as a reversed proxy in the front of Tomcat**

In computer networks, a **reverse proxy** is a type of proxy server that retrieves resources on behalf of a client from one or more servers. These resources are then returned to the client as if they originated from the Web server itself

## Singleton

In general we follow below steps to create a singleton class:

- Override the private constructor to avoid any new object creation with new operator.
- Declare a private static instance of the same class
- Provide a public static method that will return the singleton class instance variable. If the variable is not initialized then initialize it or else simply return the instance variable.

Using above steps I have created a singleton class that looks like below:

```java

public class ASingleton {

	private static ASingleton instance = null;

	private ASingleton() {
	}

	public static ASingleton getInstance() {
		if (instance == null) {
			instance = new ASingleton();
		}
		return instance;
	}

}
```

In the above code, getInstance() method is **NOT** thread safe. Multiple threads can access it at the same time and for the first few threads when the instance variable is not initialized, multiple threads can enters the if loop and create multiple instances and break our singleton implementation.

1. Create the instance variable at the time of class loading.


```java

public class ASingleton {

	private static ASingleton instance = new ASingleton();

	private ASingleton() {
	}

	public static ASingleton getInstance() {
		if (instance == null) {
			instance = new ASingleton();
		}
		return instance;
	}

}
```

Pros:

- Thread safety without synchronization
- Easy to implement

Cons:

- Early creation of resource that might not be used in the application.
- The client application can’t pass any argument, so we can’t reuse it. For example, having a generic singleton class for database connection where client application supplies database server properties.

Another similar pattern:

```java
public class Singleton {
    private Singleton() {
        //lots of initialization code
    }

    private static class SingletonHolder {
        public static final Singleton instance = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }
}
```


2. Synchronize the getInstance() method

[Synchronized Methods](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html)

```java

public class ASingleton {

	private static ASingleton instance = null;

	private ASingleton() {
	}

	public static synchronized ASingleton getInstance() { // Notice keyword `synchronized`
		if (instance == null) {
			instance = new ASingleton();
		}
		return instance;
	}

}
```

Pros:

- Thread safety is guaranteed.
- Client application can pass parameters
- Lazy initialization achieved

Cons:

- Slow performance because of locking overhead.
- Unnecessary synchronization that is not required once the instance variable is initialized.


3. Use synchronized block inside the if loop

[Intrinsic Locks and Synchronization](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)

```java
public class ASingleton{

	private static ASingleton instance= null;
	private static Object mutex= new Object();
	private ASingleton(){
	}

	public static ASingleton getInstance(){
		if(instance==null){
			synchronized (mutex){ // notice synchronized used here
				if(instance==null) instance= new ASingleton();
			}
		}
		return instance;
	}

}
```

Pros:

- Thread safety is guaranteed
- Client application can pass arguments
- Lazy initialization achieved
- Synchronization overhead is minimal and applicable only for first few threads when the variable is null.

Cons:

- Extra if condition

## Thread and Thread pooling

https://stackify.com/java-thread-pools/#:~:text=The%20ThreadPoolExecutor%20provides%20more%20control,executed%20on%20their%20own%20threads.

#### ThreadPoolExecutor: how does it reuse threads

https://stackoverflow.com/questions/22112469/threadpoolexecutor-how-does-it-reuse-threads#:~:text=It's%20very%20simple%20%2D%20in%20essence,task%20and%20then%20sleep%20again.&text=A%20BlockingQueue%20uses%20wait%20and,they%20are%20not%20processing%20tasks.

It's very simple - in essence the Threads sleep, waiting to be woken by a task - they run that task and then sleep again.

```java
public static void main(final String[] args) throws Exception {
    final BlockingQueue<Runnable> blockingQueue = new LinkedBlockingDeque<>();
    final Thread t = new Thread(new Runnable() {

        @Override
        public void run() {
            while (true) {
                try {
                    blockingQueue.take().run();
                } catch (InterruptedException ex) {
                    return;
                }
            }
        }
    });
    t.start();
    blockingQueue.add(new Runnable() {

        @Override
        public void run() {
            System.out.println("Task 1");
        }
    });
    blockingQueue.add(new Runnable() {

        @Override
        public void run() {
            System.out.println("Task 2");
        }
    });
}
```

The `BlockingQueue` will block a `Thread` while it is empty. When I add an item, and Thread(s) currently blocked are awoken and one will take the task (LinkedBlockingDeque is thread safe). When the Thread is done with the task it goes back to sleep.

The JavaDoc for ThreadPoolExecutor describes the logic in detail. All of the constructors for ThreadPoolExecutor take a BlockingQueue<Runnable> - this should give you a hint as so how the logic works.

NB: This is not the same as busy waiting. A BlockingQueue uses wait and notify to suspend and wake Threads, this means that the Threads in the pool are not doing any work when they are not processing tasks. A busy wait based approach would not work because the Threads would block up all the CPU cores with their polling not allowing the program to proceed (or at least severely impairing it).

#### The Event Dispatch Thread

Swing event handling code runs on a special thread known as the event dispatch thread. Most code that invokes Swing methods also runs on this thread. This is necessary because most Swing object methods are not "thread safe": invoking them from multiple threads risks thread interference or memory consistency errors. Some Swing component methods are labelled "thread safe" in the API specification; these can be safely invoked from any thread. All other Swing component methods must be invoked from the event dispatch thread. Programs that ignore this rule may function correctly most of the time, but are subject to unpredictable errors that are difficult to reproduce.

It's useful to think of the code running on the event dispatch thread as a series of short tasks. Most tasks are invocations of event-handling methods, such as ActionListener.actionPerformed. Other tasks can be scheduled by application code, using invokeLater or invokeAndWait. Tasks on the event dispatch thread must finish quickly; if they don't, unhandled events back up and the user interface becomes unresponsive.

If you need to determine whether your code is running on the event dispatch thread, invoke `javax.swing.SwingUtilities.isEventDispatchThread`.

## Lambda expression

https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html

## Annotation

https://medium.com/@dmi3coder/pythons-decorators-vs-java-s-annotations-same-thing-2b1ef12e4dc5#:~:text=Annotation%20is%2C%20in%20simple%20words,some%20work%2C%20read%20this%20metadata.&text=So%20Java's%20annotation%20just%20adds,modify%20usage%20of%20the%20function.

https://docs.oracle.com/javase/tutorial/java/annotations/predefined.html

In the Java Collections Framework, the class `List<MyClass>` represents an ordered collection of objects of type `MyClass`. Upper bounds are specified using extends: A `List<? extends MyClass>` is a list of objects of some subclass of `MyClass`, i.e. any object in the list is guaranteed to be of type `MyClass`, so one can iterate over it using a variable of type `MyClass`

```java
public void doSomething(List<? extends MyClass> list) { // MyClass is parent class, ? is child class
    for (MyClass object : list) { // OK, because iterate over parent class
        // do something
    }
}
```

However, it is not guaranteed that one can add any object of type `MyClass` to that list:

```java
public void doSomething(List<? extends MyClass> list) { // MyClass is parent class, ? is child class
    MyClass m = new MyClass();
    list.add(m); // Compile error, list can be a list of child classes, adding parent can be errors
}
```

The converse is true for lower bounds, which are specified using super: A List<? super MyClass> is a list of objects of some superclass of MyClass, i.e. the list is guaranteed to be able to contain any object of type MyClass, so one can add any object of type MyClass:

```java
public void doSomething(List<? super MyClass> list) { // MyClass is child class, ? is parent
    MyClass m = new MyClass();
    list.add(m); // OK
}
```

However, it is not guaranteed that one can iterate over that list using a variable of type MyClass:

```java
public void doSomething(List<? super MyClass> list) { // MyClass is child class, ? is parent
    for (MyClass object : list) { // Compile error
        // do something
    }
}
```

In order to be able to do both add objects of type MyClass to the list and iterate over it using a variable of type MyClass, a List<MyClass> is needed, which is the only type of List that is both List<? extends MyClass> and List<? super MyClass>

## Create an Annotation

https://www.baeldung.com/java-custom-annotation

https://github.com/eugenp/tutorials/blob/master/core-java-modules/core-java-annotations/src/main/java/com/baeldung/customannotations/ObjectToJsonConverter.java




## wildcard

`?` is wildcard, we can see them in `Class<?>`

https://stackoverflow.com/questions/9921676/what-does-class-mean-in-java
https://en.wikipedia.org/wiki/Wildcard_%28Java%29

## Difference betwee wildcard(`?`) and `T`?

https://www.quora.com/What-is-the-difference-between-using-T-and-a-wildcard-in-Java-Generics#:~:text=If%20you%20have%20only%20one,parameters%20just%20support%20upper%20bounds.

There are certain places, where wildcards, and type parameters do the same thing. But there are also certain places, where you have to use type parameters.

If you want to enforce some relationship on the different types of method arguments, you can't do that with wildcards, you have to use type parameters.
suppose you want to ensure that the src and dest list passed to copy() method should be of same parameterized type, you can do it with type parameters like so:

```java
public static <T extends Number> void copy(List<T> dest, List<T> src)
```

Here, you are ensured that both dest and src have same parameterized type for List. So, it's safe to copy elements from src to dest.

But, if you go on to change the method to use wildcard:

```java
public static void copy(List<? extends Number> dest, List<? extends Number> src)
```

it won't work as expected. In 2nd case, you can pass `List<Integer>` and `List<Float>` as dest and src. So, moving elements from src to dest wouldn't be type safe anymore. If you don't need such kind of relation, then you are free not to use type parameters at all.

Some other difference between using wildcards and type parameters are:

1. If you have only one parameterized type argument, then you can use wildcard, although type parameter will also work.
Type parameters support multiple bounds, wildcards don't.
2. Wildcards support both upper and lower bounds, type parameters just support upper bounds. So, if you want to define a method that takes a List of type Integer or it's super class, you can do:

```java
public void print(List<? super Integer> list) // OK
```

but you can't use type parameter:

```java
 public <T super Integer> void print(List<T> list) // Won't compile
 ```

## Optional

https://stackify.com/optional-java/

## Factory pattern and Builder pattern

https://stalk-calvin.github.io/blog/2016/10/15/builder-pattern.html#:~:text=Builder%20pattern%20is%20an%20object,object%20is%20being%20created%20though.

## Java Database Connectivity (JDBC)

To put it simply: Java (J2SE/J2EE) provides `java.sql.*` as the standard way to connect to a database. The issue is that these classes do not really know how to connect to each specific database in the model, they are oriented to the programmer.

To connecto to each database, you need to put in its Driver. The Oracle driver will know how to connect to the Oracle database, the mysql driver will know how to connect to MySQL. `java.sql` will know how to use each Driver, so by just using it you do not need to know the internals of each Driver.

https://en.wikipedia.org/wiki/JDBC_driver

https://en.wikipedia.org/wiki/Java_Database_Connectivity

### Connect to MySql

https://www.ntu.edu.sg/home/ehchua/programming/java/JDBC_Basic.html

### Close database connecion

https://stackoverflow.com/questions/2121805/problem-with-not-closing-db-connection-while-debugging

### JDBC vs JPA

In layman's terms:

JDBC is a standard for Database Access
JPA is a standard for ORM

JDBC is a standard for connecting to a DB directly and running SQL against it - e.g SELECT * FROM USERS, etc. Data sets can be returned which you can handle in your app, and you can do all the usual things like INSERT, DELETE, run stored procedures, etc. It is one of the underlying technologies behind most Java database access (including JPA providers).

One of the issues with traditional JDBC apps is that you can often have some crappy code where lots of mapping between data sets and objects occur, logic is mixed in with SQL, etc.

JPA is a standard for Object Relational Mapping. This is a technology which allows you to map between objects in code and database tables. This can "hide" the SQL from the developer so that all they deal with are Java classes, and the provider allows you to save them and load them magically. Mostly, XML mapping files or annotations on getters and setters can be used to tell the JPA provider which fields on your object map to which fields in the DB. The most famous JPA provider is Hibernate, so it's a good place to start for concrete examples.

Other examples include OpenJPA, toplink, etc.

Under the hood, Hibernate and most other providers for JPA write SQL and use JDBC to read and write from and to the DB.

### DAO

https://www.baeldung.com/java-dao-pattern#:~:text=The%20Data%20Access%20Object%20(DAO,mechanism)%20using%20an%20abstract%20API.

The Data Access Object (DAO) pattern is a structural pattern that allows us to isolate the application/business layer from the persistence layer (usually a relational database, but it could be any other persistence mechanism) using an abstract API.

The functionality of this API is to hide from the application all the complexities involved in performing CRUD operations in the underlying storage mechanism. This permits both layers to evolve separately without knowing anything about each other.

### DAO vs Repository

https://blog.sapiensworks.com/post/2012/11/01/Repository-vs-DAO.aspx

Back to Repository and DAO, in conclusion, they have similar intentions only that the Repository is a higher level concept dealing directly with business/domain objects, while DAO is more lower level, closer to the database/storage dealing only with data. A (micro)ORM is a DAO that is used by a Repository. For data-centric apps, a repository and DAO are interchangeable because the 'business' objects are simple data.

https://medium.com/@vngphm/repositories-pattern-vs-dao-pattern-765470e73cf3

DAO maps to a table, if in a controller, you need to access two tables, one is local, the other is a rest API, you can create a Respository that read both DAOs and aggregate them together.

You can also have a Service in front of Respository.

So the level would be (in case of model Order)

- OrderController
- OrderService
- OrderRepository ----------
                            |---> together can be an unit of work
- OrderDAO -----------------
- JPA/EntityManager (Hibernet)
- JDBC
- Database


### Bootstrapping JPA Programmatically in Java

https://www.baeldung.com/java-bootstrap-jpa

### Hibernate

https://docs.jboss.org/hibernate/orm/4.0/devguide/en-US/html/ch01.html

### DBCP

database connection pooling

https://commons.apache.org/proper/commons-dbcp/download_dbcp.cgi

### Difference bettwen JDBC and ODBC

https://www.quora.com/What-is-the-key-difference-between-JDBC-and-ODBC

### Difference between EntityManagerFactory and SessionFactory in Hibernate

https://stackoverflow.com/questions/5640778/hibernate-sessionfactory-vs-jpa-entitymanagerfactory


### Difference Between import and class.forName in java

https://stackoverflow.com/questions/2092659/what-is-the-difference-between-class-forname-and-class-forname-newinstanc

1 : `import`

```
==> loads the class when you call any instance of it or call anything by class reference
==> loads the class when call is made
```

2 : `Class.forName("");`

```
==> loads the class in the jvm immediately
```

difference can be seen by if a class has static block

```
==> import will not call the static block
==> Class.forName("") will call the static block
```

In JDBC earlier than 4.0 case

```
===> Driver class when loaded by Class.forName("") , executes its static block , which published the driver
==> Simply importing the Driver class wont execute the static block and thus your Driver will not be published for connection objects to be created
```

## Spring

#### Spring IoC/Context/BeanFactory

#### Spring Data

#### Stream with spring cloud stream

#### Spring Boot Hibernate example

https://java2blog.com/spring-boot-hibernate-example/

#### @SpringBootApplication annotation

The @SpringBootApplication annotation is equivalent to using @Configuration, @EnableAutoConfiguration and @ComponentScan with their default attributes.

#### CreatedOn and UpdatedOn with Hibernate

https://stackoverflow.com/questions/221611/creation-timestamp-and-last-update-timestamp-with-hibernate-and-mysql

#### Use auto-increment primary key using hibernate

https://thorben-janssen.com/hibernate-tips-use-auto-incremented-column-primary-key/#:~:text=If%20you%20want%20to%20use,IDENTITY.&text=If%20you%20now%20persist%20a,generate%20the%20primary%20key%20value.

#### Spring boot with JSP

https://htr3n.github.io/2018/12/jsp-spring-boot/

#### @RequestBody && @ResponseBody

https://www.baeldung.com/spring-request-response-body

#### @ResponseStatus

https://www.baeldung.com/spring-response-status


#### @Resource, @Inject and @Autowired

https://www.baeldung.com/spring-annotations-resource-inject-autowire

https://www.linkedin.com/pulse/difference-between-inject-vs-autowire-resource-pankaj-kumar/

#### @Component

`@Component` is the most generic Spring annotation. A Java class decorated with` @Component` is found during classpath scanning and registered in the context as a Spring bean. `@Service`, `@Repository`, and `@Controller` are specializations of `@Component`, which are used for more specific cases.

`@ComponentScan` ensures that the classes decorated with `@Component` are found and registered as Spring beans. `@ComponentScan` is automatically included with `@SpringBootApplication`.

#### @Bean

`@Bean` servers a similar purpose as `@Component`. It is not autodetected. Methods decorated with `@Bean` produce a bean to be managed by the Spring container during configuration stage.

#### Difference between @Bean and @Component

@Component and @Bean do two quite different things, and shouldn't be confused.

@Component (and @Service and @Repository) are used to auto-detect and auto-configure beans using classpath scanning. There's an implicit one-to-one mapping between the annotated class and the bean (i.e. one bean per class). Control of wiring is quite limited with this approach, since it's purely declarative.

@Bean is used to explicitly declare a single bean, rather than letting Spring do it automatically as above. It decouples the declaration of the bean from the class definition, and lets you create and configure beans exactly how you choose.

#### When use @Bean?

When should you use @Bean?

Sometimes automatic configuration is not an option. When? Let's imagine that you want to wire components from 3rd-party libraries (you don't have the source code so you can't annotate its classes with @Component), so automatic configuration is not possible.

@Bean Methods in @Configuration Classes

Typically, @Bean methods are declared within @Configuration classes. In this case, bean methods may reference other @Bean methods in the same class by calling them directly. This ensures that references between beans are strongly typed and navigable. Such so-called 'inter-bean references' are guaranteed to respect scoping and AOP semantics, just like getBean() lookups would. These are the semantics known from the original 'Spring JavaConfig' project which require CGLIB subclassing of each such configuration class at runtime. As a consequence, @Configuration classes and their factory methods must not be marked as final or private in this mode. For example:

```java
 @Configuration
 public class AppConfig {

     @Bean
     public FooService fooService() {
         return new FooService(fooRepository());
     }

     @Bean
     public FooRepository fooRepository() {
         return new JdbcFooRepository(dataSource());
     }

     // ...
}
```


@Bean methods may also be declared within classes that are not annotated with @Configuration. For example, bean methods may be declared in a @Component class or even in a plain old class. In such cases, a @Bean method will get processed in a so-called 'lite' mode.

Bean methods in lite mode will be treated as plain factory methods by the container (similar to factory-method declarations in XML), with scoping and lifecycle callbacks properly applied. The containing class remains unmodified in this case, and there are no unusual constraints for the containing class or the factory methods.

In contrast to the semantics for bean methods in @Configuration classes, 'inter-bean references' are not supported in lite mode. Instead, when one @Bean-method invokes another @Bean-method in lite mode, the invocation is a standard Java method invocation; Spring does not intercept the invocation via a CGLIB proxy. This is analogous to inter-@Transactional method calls where in proxy mode, Spring does not intercept the invocation — Spring does so only in AspectJ mode.

For example:

```java
 @Component
 public class Calculator {
     public int sum(int a, int b) {
         return a+b;
     }

     @Bean
     public MyBean myBean() {
         return new MyBean();
     }
 }

```


Example of @Bean Lite Mode and @Bean in a @Configuration class

```java
@Configuration
public static class Config {

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean());
    }
}
```

```java
@Component
public static class Config {

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean());
    }
}
```

The first piece of code works fine, and as expected, SimpleBeanConsumer will get a link to singleton SimpleBean. But unfortunately, it doesn’t work in a signed enviroment.

The second configuration is totally incorrect because spring will create a singleton bean of SimpleBean, but SimpleBeanConsumer will obtain another instance of SimpleBean which is out of the spring context control.

The reason for this behaviour can be explained as follows:

If you use @Configuration, all methods marked as @Bean will be wrapped into a CGLIB wrapper which works as if it’s the first call of this method, then the original method’s body will be executed and the resulting object will be registered in the spring context. All further calls just return the bean retrieved from the context.

In the second code block above, new SimpleBeanConsumer(simpleBean()) just calls a pure java method. To correct the second code block, we can do something like this:

```java
@Component
public static class Config {
    @Autowired
    SimpleBean simpleBean;

    @Bean
    public SimpleBean simpleBean() {
        return new SimpleBean();
    }

    @Bean
    public SimpleBeanConsumer simpleBeanConsumer() {
        return new SimpleBeanConsumer(simpleBean);
    }
}
```

#### @Configuration

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html

```java
@Target(value=TYPE)
@Retention(value=RUNTIME)
@Documented
@Component
public @interface Configuration
```

Indicates that a class declares one or more `@Bean` methods and may be processed by the Spring container to generate bean definitions and service requests for those beans at runtime, for example:

#### @ConfigurationProperties

http://zetcode.com/springboot/configurationproperties/

Spring Boot `@ConfigurationProperties` tutorial shows how to bind properties to an object with `@ConfigurationProperties` in a Spring Boot application.

`@ConfigurationProperties` allows to map the entire Properties and Yaml files into an object easily. It also allows to validate properties with JSR-303 bean validation. By default, the annotation reads from the `application.properties` file. The source file can be changed with `@PropertySource` annotation.

```bash
# resources/application.properties
spring.main.banner-mode=off

app.colour=steelblue
app.lang=en
app.theme=dark
```

```java
package com.zetcode.conf;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConfigurationProperties(prefix = "app")
public class AppProperties {

    private String colour;
    private String lang;
    private String theme;

    public String getColour() {
        return colour;
    }

    public void setColour(String colour) {
        this.colour = colour;
    }

    public String getLang() {
        return lang;
    }

    public void setLang(String lang) {
        this.lang = lang;
    }

    public String getTheme() {
        return theme;
    }

    public void setTheme(String theme) {
        this.theme = theme;
    }
}
````

#### @Profile

Profiles are a core feature of the framework — allowing us to map our beans to different profiles — for example, **dev, test, and prod**.

```java
@Component
@Profile("dev")
public class DevDatasourceConfig
```

As a quick sidenote, profile names can also be prefixed with a NOT operator, e.g., `!dev`, to exclude them from a profile.

In the example, the component is activated only if dev profile is not active:

```java
@Component
@Profile("!dev")
public class DevDatasourceConfig
```

Pass `-Dspring.profiles.active=dev` to the VM Options to activate a profile


#### Aspect-Oriented Programming(AOP )

https://docs.jboss.org/aop/1.0/aspect-framework/userguide/en/html/what.html

**1.1. What is it?**

An aspect is a common feature that's typically scattered across methods, classes, object hierarchies, or even entire object models. It is behavior that looks and smells like it should have structure, but you can't find a way to express this structure in code with traditional object-oriented techniques.

For example, metrics is one common aspect. To generate useful logs from your application, you have to (often liberally) sprinkle informative messages throughout your code. However, metrics is something that your class or object model really shouldn't be concerned about. After all, metrics is irrelevant to your actual application: it doesn't represent a customer or an account, and it doesn't realize a business rule. It's simply orthogonal.

In AOP, a feature like metrics is called a crosscutting concern, as it's a behavior that "cuts" across multiple points in your object models, yet is distinctly different. As a development methodology, AOP recommends that you abstract and encapsulate crosscutting concerns.

For example, let's say you wanted to add code to an application to measure the amount of time it would take to invoke a particular method. In plain Java, the code would look something like the following.

```java
public class BankAccountDAO
{
  public void withdraw(double amount)
  {
    long startTime = System.currentTimeMillis();
    try
    {
      // Actual method body...
    }
    finally
    {
      long endTime = System.currentTimeMillis() - startTime;
      System.out.println("withdraw took: " + endTime);
    }
  }
}
```

While this code works, there are a few problems with this approach:

- It's extremely difficult to turn metrics on and off, as you have to manually add the code in the try>/finally block to each and every method or constructor you want to benchmark.
- The profiling code really doesn't belong sprinkled throughout your application code. It makes your code bloated and harder to read, as you have to enclose the timings within a try/finally block.
- If you wanted to expand this functionality to include a method or failure count, or even to register these statistics to a more sophisticated reporting mechanism, you'd have to modify a lot of different files (again).

This approach to metrics is very difficult to maintain, expand, and extend, because it's dispersed throughout your entire code base. And this is just a tiny example! In many cases, OOP may not always be the best way to add metrics to a class.

Aspect-oriented programming gives you a way to encapsulate this type of behavior functionality. It allows you to add behavior such as metrics "around" your code. For example, AOP provides you with programmatic control to specify that you want calls to BankAccountDAO to go through a metrics aspect before executing the actual body of that code.

### Maven

#### Difference between dependencies management and dependencies**

https://jainamit333.wordpress.com/2017/08/05/difference-between-dependency-management-and-dependencies-in-maven/

#### mvnw and mvnw.cmd

https://stackoverflow.com/questions/38723833/what-is-the-purpose-of-mvnw-and-mvnw-cmd-files

These files are from Maven wrapper. It works similarly to the Gradle wrapper.

This allows you to run the Maven project without having Maven installed and present on the path. It downloads the correct Maven version if it's not found (as far as I know by default in your user home directory).

The mvnw file is for Linux (bash) and the mvnw.cmd is for the Windows environment.

#### Difference between Snapshot and Release

A **Snapshot** version in Maven is one that has not been released.

The idea is that before a 1.0 release (or any other release) is done, there exists a 1.0-SNAPSHOT. That version is what might become 1.0. It's basically "1.0 under development". This might be close to a real 1.0 release, or pretty far (right after the 0.9 release, for example).

The difference between a "real" version and a snapshot version is that snapshots might get updates. That means that downloading 1.0-SNAPSHOT today might give a different file than downloading it yesterday or tomorrow.

Usually, snapshot dependencies should only exist during development and no released version (i.e. no non-snapshot) should have a dependency on a snapshot version.

A **Release** build is the final build for a version which does not change.

#### Snapshot dependencies behavior

When you build an application, Maven will search for dependencies in the local repository. If a stable version is not found there, it will search the remote repositories (defined in settings.xml or pom.xml) to retrieve this dependency. Then, it will copy it into the local repository, to make it available for the next builds.

For example, a foo-1.0.jar library is considered as a stable version, and if Maven finds it in the local repository, it will use this one for the current build.

Now, if you need a foo-1.0-SNAPSHOT.jar library, Maven will know that this version is not stable and is subject to changes. That's why Maven will try to find a newer version in the remote repositories, even if a version of this library is found on the local repository. However, this check is made only once per day. That means that if you have a foo-1.0-20110506.110000-1.jar (i.e. this library has been generated on 2011/05/06 at 11:00:00) in your local repository, and if you run the Maven build again the same day, Maven will not check the repositories for a newer version.

Maven provides you a way to change this update policy in your repository definition:

```xml
<repository>
    <id>foo-repository</id>
    <url>...</url>
    <snapshots>
        <enabled>true</enabled>
        <updatePolicy>XXX</updatePolicy>
    </snapshots>
</repository>
```

where XXX can be:

- always: Maven will check for a newer version on every build;
- daily, the default value;
- interval:XXX: an interval in minutes (XXX)
- never: Maven will never try to retrieve another version. It will do that only if it doesn't exist locally. With the configuration, SNAPSHOT version will be handled as the stable libraries.

#### Multi-Module project

A multi-module project is built from an aggregator POM that manages a group of submodules. In most cases, the aggregator is located in the project's root directory and must have packaging of type `pom`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.some.assets</groupId>
    <artifactId>some-name</artifactId>
    <version>1.0.1-SNAPSHOT</version>
    <packaging>pom</packaging> <!--packaging type pom-->
</project>
```

Now, the submodules are regular Maven projects, and they can be built separately or through the aggregator POM.

By building the project through the aggregator POM, each project that has packaging type different than pom will result in a built archive file.

**Benefits of using multi-modules**

The significant advantage of using this approach is that we may reduce duplication.

The significant advantage of using this approach is that we may reduce duplication.

Let's say we have an application which consists of several modules, let it be a front-end module and a back-end module. Now, we work on both of them and change functionality which affects the two. In that case, without a specialized build tool, we'll have to build both components separately or write a script which would compile the code, run tests and show the results. Then, after we get even more modules in the project, it will become harder to manage and maintain.

Besides, in the real world, projects may need certain Maven plugins to perform various operations during build lifecycle, share dependencies and profiles or include other BOM projects.

Therefore, when leveraging multi-modules, we can build our application's modules in a single command and if the order matters, Maven will figure this out for us. Also, we can share a vast amount of configuration with other modules.
