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

