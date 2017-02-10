# Java-Notes
Some Java Notes


## List

Importing library: `import java.util.List`.

It's an iterface. `ArrayList`, `LinkedList` implement it.


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

In Java, You always return a reference (unless returned value is a primitive type such as int, float, char, ...).

So, if you don't want the returned object to be modified, you must return a full copy of it (you could use Clonable interface and clone method if your class defines it). Or the object is designed to be immutable.
