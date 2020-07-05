### Binary Search - Efficient algorithm to search a sorted array

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
        for (int i = 1; i < N; i++)
            // Iterate over array in Reverse Order.
        	for (int j = i; j > 0; j --)
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

### Merge 

Abstract in-place merge 
- Merge a[low,middle] with a[middle,high]
- Use auxiliary array for result.
- Copy back when merge is complete

#### Java Implementation

```Java
private static String[] aux;
public static void merge(String[] a, int low, int middle, int high) {
    int i = low, j = middle, N = high - low;
    // iterate through first array
    for (int k = 0; k < N; k++) {
        if (i == middle) aux[k] = a[j++]; // if array[low to middle] is empty
        else if (j == high) aux[k] = a[i++]; // if array[middle to high] is empty
        else if (a[j].compareTo(a[i]) < 0) aux[k] = a[j++]; //
        else aux[k] = a[i++];
    }
    // copy back from auxiliary into original array.
    for (int k = 0; k < N; k++) {
        a[low + k] = aux[k];
    }
}
```

### Mergesort - Efficient algorithm to sort an array

> - Divide array into two halves.
> - Recursively sort each half.
> - Merge two halves to make a sorted whole.

```Java
public class Merge
{
    private static String[] aux;
    public static void merge(String[] a, int low, int middle, int high) { // Refer to Merge }
	public static void sort(String[] a)
    {
        aux = new String[a.length]; // Allocate once;
        sort(a, 0, a.length);
    }
    public static void sort(String[] a, int low, int high)
    {
        int N = high - low;
        if ( N <= 1) return;
        int middle = low + N/2;
        sort(a, low, middle);
        sort(a, middle,high);
       	merge(a, low, middle, high);
    }
    public static void main(String[] arg) 
    {
        In in = new In(arg[0]);
        String[] input = in.readAllStrings();
        sort(input);
        for (int i = 0; i < input.length; i++) {
            StdOut.println(input[i]);
        }
    }
}
```

### Longest Common Prefix (LCP)

> Given: Two strings string s and t.
>
> Find the longest substring that appears at the beginning of both strings.

#### Java Implementation

```java
private static String lcp(String s, String t)
{
    int N = Math.min(s.length(), t.length());
    for (int i = 0; i < N ; i++)
    {
        if (s.charAt(i) != t.charAt(i))
            return s.substring(0,i);
        return s.substring(0.N);
    }
}
```



### Longest Repeated Substring (LRS)

#### Brute - Force Implementation

```Java
public class LRS
{
	public static String lcp(String s) { /* Refer to LCP */ }

	public static String lrs(String s)
    {
        int N = s.length();
        String lrs = "";
        for (int i = 0; i < N; i++)
            for (int j = i+1; j < N; j++)
        	{
            	String x = lcp(s.substring(i,N), s.substring(j,N));
                if (x.length() > lrs.length()) lrs = x;
        	}
        return lrs;
    }
	public static void main(String[] args)
    {
        String s = StdIn.readAll();
        StdOut.println(lrs(s));
    }
}
```

#### Efficient solution with Sorting - Implementation

> 1. Form suffix strings
> 2. Sort suffix strings
> 3. Find longest LCP among adjacent entries

```Java
public static String lrs(String s)
{
	int N = s.length();
    String[] suffixes = new String[N];
    for (int i = 0; i < N; i++)
        suffixes[i] = s.substring(i, N);
    
    Merge.sort(suffixes);
    
    String lrs = "";
    for (int i = 0; i < N-1; i++)
    {
        String x = lcp(suffixes[i], suffixes[i+1]);
        if (x.length() > lrs.length()) lrs = x;
    }
    return x
}
```

