# Java-Notes
Some Java Notes

## Just-In-Time(JIT) Overview

The Just-In-Time (JIT) compiler is a component of the Java™ Runtime Environment that improves the performance of Java applications at run time.

Java programs consists of classes, which contain platform-neutral bytecodes that can be interpreted by a JVM on many different computer architectures. At run time, the JVM loads the class files, determines the semantics of each individual bytecode, and performs the appropriate computation. The additional processor and memory usage during interpretation means that a Java application performs more slowly than a native application. The JIT compiler helps improve the performance of Java programs by compiling bytecodes into native machine code at run time.

The JIT compiler is enabled by default, and is activated when a Java method is called. The JIT compiler compiles the bytecodes of that method into native machine code, compiling it "just in time" to run. When a method has been compiled, the JVM calls the compiled code of that method directly instead of interpreting it. Theoretically, if compilation did not require processor time and memory usage, compiling every method could allow the speed of the Java program to approach that of a native application.

JIT compilation does require processor time and memory usage. When the JVM first starts up, thousands of methods are called. Compiling all of these methods can significantly affect startup time, even if the program eventually achieves very good peak performance.

In practice, methods are not compiled the first time they are called. For each method, the JVM maintains a call count, which is incremented every time the method is called. The JVM interprets a method until its call count exceeds a JIT compilation threshold. Therefore, often-used methods are compiled soon after the JVM has started, and less-used methods are compiled much later, or not at all. The JIT compilation threshold helps the JVM start quickly and still have improved performance. The threshold has been carefully selected to obtain an optimal balance between startup times and long term performance.

After a method is compiled, its call count is reset to zero and subsequent calls to the method continue to increment its count. When the call count of a method reaches a JIT recompilation threshold, the JIT compiler compiles it a second time, applying a larger selection of optimizations than on the previous compilation. This process is repeated until the maximum optimization level is reached. The busiest methods of a Java program are always optimized most aggressively, maximizing the performance benefits of using the JIT compiler. The JIT compiler can also measure operational data at run time, and use that data to improve the quality of further recompilations.

The JIT compiler can be disabled, in which case the entire Java program will be interpreted. Disabling the JIT compiler is not recommended except to diagnose or work around JIT compilation problems.

## List

Importing library: `import java.util.List`.

It's an interface. `ArrayList`, `LinkedList` implement it.


## ArrayList

Importing library: `import java.util.ArrayList`

Implements `List`

## LinkedList

Importing library: `import java.util.LinkedList`

Implements `List`

## LinkedList VS. ArrayList

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


## Iteration for `Map` and `Set`

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
         \________/	     \______/    	\
			                             +------------------+
          unchecked	     checked	     | RuntimeException |
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
	// The FileWriter constructor throws IOException, which must be caught.
        PrintWriter out = new PrintWriter(new FileWriter("OutFile.txt"));

        for (int i = 0; i < SIZE; i++) {
            // The get(int) method throws IndexOutOfBoundsException, which must be caught.
            out.println("Value at: " + i + " = " + list.get(i));
        }
        out.close();
    }
}
```

`new FileWriter("OutFile.txt")` is a call to a constructor. The constructor initializes an output stream on a file. If the file cannot be opened, the constructor throws an `IOException`. `list.get(i)` is a call to the `ArrayList` class's `get` method, which throws an `IndexOutOfBoundsException` if the value of its argument is too small (less than 0) or too large (more than the number of elements currently contained by the `ArrayList`).

The class do not compile because we don't handle the exceptions, so to handle them we do:

```
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

## Override and Hiding Methods

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

## Inheritance


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

### Web Server, Nginx

**You can use Nginx as a reversed proxy in the front of Tomcat**

In computer networks, a **reverse proxy** is a type of proxy server that retrieves resources on behalf of a client from one or more servers. These resources are then returned to the client as if they originated from the Web server itself

### Java Database Connectivity (JDBC)

To put it simply: Java (J2SE/J2EE) provides `java.sql.*` as the standard way to connect to a database. The issue is that these classes do not really know how to connect to each specific database in the model, they are oriented to the programmer.

To connecto to each database, you need to put in its Driver. The Oracle driver will know how to connect to the Oracle database, the mysql driver will know how to connect to MySQL. `java.sql` will know how to use each Driver, so by just using it you do not need to know the internals of each Driver.

https://en.wikipedia.org/wiki/JDBC_driver

https://en.wikipedia.org/wiki/Java_Database_Connectivity

### Connect to MySql

https://www.ntu.edu.sg/home/ehchua/programming/java/JDBC_Basic.html

### Close database connecion

https://stackoverflow.com/questions/2121805/problem-with-not-closing-db-connection-while-debugging

### Difference bettwen JDBC and ODBC

https://www.quora.com/What-is-the-key-difference-between-JDBC-and-ODBC
https://www.quora.com/What-is-the-key-difference-between-JDBC-and-ODBC

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

	private static ASingleton instance = new ASingleton;

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

