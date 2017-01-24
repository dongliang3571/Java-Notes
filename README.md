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




