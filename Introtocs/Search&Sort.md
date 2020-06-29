### Binary Search Arithmetic

Notation. a[low,high] means a[low], a[low+1] ... a[high-1] - **DOES NOT INCLUDE a[high]**

```
Search in a[low,high]

e.g. Low - 10 , High , 30

middle = low + (high-low)/2 
       = 10 + ( 30 - 10 ) / 2
       = 20
       
Lower half = a[low,middle]

Upper half = a[middle+1, high]
```

#### Java Implementation

> Recursive Routine: Array goes from zero to a.length -1
>
> Termination Condition -
>
> if high less than or equal to low, that means nothing in the array.



```Java
public static int search(String key, String[] a) 
{
 return search(key, a, 0, a.length);
}
public static int search(String key, String[] a, int low, int high)
{
	// termination condition
	if( high <= low) return -1;
    int middle = low + (high - low) / 2;
    /* Compare element against key: 
    	if key < a[middle], return -1
    	if key = a[middle], return 0
    	if key > a[middle], return 1	
    */
	int compare = a[middle].compareTo(key);
    if (compare > 0) return search(key, a, low, middle);
    else if (compare < 0) return search(key, a, middle +1 , high);
    else return middle;
}
```

[String compareTo()](https://www.javatpoint.com/java-string-compareto)

### Insertion Sort Algorithm

> - Move down through array
> - Each item bubbles up above the larger one above it.
> - Everything **above** current item is in order.
> - Everything **below** current item is untouched.

#### Java Implementation

```Java
public class Insertion
{
    public static void sort(String[] a)
    {
        int N = a.length;
        // Iterate through array
        for (int i =1; i < N; i++)
            // Iterate over array in Reverse Order.
        	for (int j = 0; j > 0; j --)
                if (a[j-1].compareTo(a[j]) > 0)
                    exchange(a, j -1, j);
        		else break;
    }
    private static void exchange(String[] a, int i, int j)
    {
        String t = a[i]; a[i] = a[j]; a[j] = t; 
    }
    public static void main(String[] args)
    {
        String[] a = StdIn.readAllStrings();
        sort(a);
        for (int i = 0; i < a.length; i++)
     		StdOut.println(a[i]);
    }
}

```

