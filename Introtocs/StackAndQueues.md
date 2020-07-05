# Stack and Queue API

- A Collection is an ADT whose values are a multiset of items, all of the same type.
- Two fundamental collection ADTS differ in just a detail of the specification of their Operations.



## Stack Operations

- Add an item to the collection - **Push**
- Remove and return the item most recently added  - **Pop**
  - LIFO - Last in First Out
- Test if collection is empty
- Return size of collection

### Stack Client Example

#### Postfix expression Evaluation - Also called Reverse Polish Notation(RPN)

**Infix** - Standard way of writing arithmetic expression, using parenthese for precedence

`(1+((2+3)*(4*5)))`

**Postfix**

`1 2 3 + 4 5 * * +`

##### Algorithm

- While input stream is nonempty, read a token
- Value: Push onto stack
- Operator: Pop operand(s), apply operator, push results

##### Java Implementation

```Java
public class Postfix {
    public static void main(String[] args) {
        Stack<Double> stack = new Stack<Double>();
        while (!StdIn.isEmpty()) {
            String token = StdIn.readString();
            if (token.equals("*")) stack.push(stack.pop() * stack.pop());
            else if (token.equals("+")) stack.push(stack.pop() + stack.pop());
            else if (token.equals("-")) stack.push(stack.pop() - stack.pop());
            else if (token.equals("/"))
                stack.push(
                        1.0 / stack.pop() * stack.pop()); 
            // 1.0/stack.pop() because you pop the 2nd operand first.
            else if (token.equals("sqrt")) stack.push(Math.sqrt(stack.pop()));
            else stack.push(Double.parseDouble(token));
        }
        StdOut.println(stack.pop());
    }
}
```

#### Strawman Implementation



## Queue Operation

- Add an item to collection at the end. - **Enqueue**

- Remove and return the item from the beginning -**Dequeue**

  - First in First Out

## Queue Client Example

```Java
public class QEx {
    public static String[] readAllStrings() {
        Queue<String> q = new Queue<String>();
        while (!StdIn.isEmpty()) {
            q.enqueue(StdIn.readString());
        }
        int N = q.size();
        String[] words = new String[N];
        for (int i = 0; i < N; i++) {
            words[i] = q.dequeue();
        }
        return words;
    }

    public static void main(String[] args) {
        String[] words = readAllStrings();
        for (int i = 0; i < words.length; i++) {
            StdOut.println(words[i]);
        }
    }
}
```

## Autoboxing

- Use a primitive type in a parameterized ADT.

#### Wrapper Types

- Each primitive type has a wrapper reference type
- Has a larger set of operations than primitive type.
  - E.g: Integer.parseInt()
- Instance of wrapper type are objects, that is why we can use it any way objects can be used. 
- Wrapper type can be used in a parametized ADT

```java
 Stack<Integer> stack = new Stack<Integer>();
 stack.push(17); // Autobox (int -> Integer)
 int a = stack.pop(); // Auto-unbox ( Integer -> int)
```

