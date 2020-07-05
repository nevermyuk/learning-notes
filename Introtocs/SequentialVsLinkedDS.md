## Sequential vs Linked

## Sequential Data Structure

- Put objects next to one another
- Machine: consecutive memory cells.
- Java: array of objects
- Fixed size, arbitrary access. <---- 'i'th element



## Linked Data Structure

- Associate with each object a link to another one
- Machine: Link is memory address of next object
- Java: Link is reference to next object
- Variable size, sequential access. <- next element

## Linked List

- Recursive data structure
- Def. A **linked list** is a null or a reference to a **node**
- Def. A **node** is a data type that contains a reference to a node
- Unwind recursion: A linked list is a sequence of nodes.

#### Representation.

- Use a private **nested class** Node to implement the node abstraction.
- For simplicity, start with nodes having two values: a string and a node

```Java
private class Node {
	private String item;
    private Node next;
}
```

