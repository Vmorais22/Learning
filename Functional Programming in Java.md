# Functional Programming in Java

## Collections

### Iterating through a List

We can go from:

```
for(int i = 0; i < friends.size(); i++) { 
    System.out.println(friends.get(i)); 
}
```
To:
```
friends.forEach(System.out::println);
```
Where:
- ``forEach`` is a higher-order function that iterates the collection.
- We use a variable ``name`` that represents each value iterated.
- We pass a lamda expression that indicates what to do for each iteration.
- We used a method reference to simplify the ``println`` method call.

### Transforming a List

In this imperative style, we created an empty list then populated it with alluppercase names, one element at a time, while iterating through the original list.

````agsl
final List<String> uppercaseNames = new ArrayList<String>();
 for(String name : friends) { 
    uppercaseNames.add(name.toUpperCase()); 
}
````
To:
````agsl
friends.stream() 
    .map(String::toUpperCase) 
    .forEach(name-> System.out.println(name));
````

Where:
- The method ``stream()`` wraps the collection into an instance of Stream and is commonly used to process collections. The idea is to be able to apply various methods, sequential or in parallel to this sequence of objects to produce the desired result.
  - Is not a data structure. It can be just read once. It takes input from a collection, array or I/O Channel.
  - Do not change the original data structure. Returns the result of all pipelined methods.
  - It has **intermediate operations** that are chained together and transform a stream into another stream (``map(), filter(), sorted()``) and **terminal operations** that return the result (``collect(), forEach(), reduce()``.
  - The format is always the same: source -> 0 to more intermediate methods -> terminal method.
- ``map()`` method is an intermediate opertaion as seen before. Is useful to transform an input collection into a new outpot collection.
  - Ensures the same number of elements of the input collection exists on the output. However, types don't have to match. In this example is a coincidence because both are Strings.
  - Lamba expression or method is passed as parameter pointing out the action to be performed on this map. In this case, set the String to uppercase.

> **Notes:**
> 
> Method references are nice replacements when the lambda expressions are really short and make simple, direct calls to either an instance method or a static method
>

### Finding elements
From:
````agsl
final List<String> startsWithN = new ArrayList<String>();
 for(String name : friends) { 
    if(name.startsWith("N")) { 
        startsWithN.add(name); 
    } 
}
````
To:
````agsl
final List<String> startsWithN = friends.stream()
    .filter(name-> name.startsWith("N")) 
    .collect(Collectors.toList());
````

- We're using ``stream()`` again to pipeline methods in the input collection.
- The ``filter()`` method expects a lambda expression that returns a boolean result. If the lambda expression returns a true, the element in context while executing that lambda expression is added to a result collection; it’s skipped otherwise. It's an intermediate operation, so it will return another Stream with only elements for which the lambda expression returned a true. In other words, ``filter()`` will return a subset.
- Lamda expressions can be set to a Predicate variable in order to reuse them in different intermediate operations (for example different ``filter()`` operations for different collections executed consecutive). This way we avoid duplication of code which improves efficency, scalability and reduces the probabilty of making errors. 
- Assign and ``final Predicate<String> startsWithN = name-> name.startsWith("N");`` instead do ``.filter(startsWithN)``

### Picking an element

Let’s create a method that will look for an element that starts with a given letter, and print it.

From:

````agsl
public static void pickName( 
    final List<String> names, final String startingLetter) { 
        String foundName = null; 
        for(String name : names) { 
            if(name.startsWith(startingLetter)) { 
                foundName = name; break; 
            } 
        } 
      System.out.print(String.format("A name starting with %s: ", startingLetter)); 
      
      if(foundName != null) { 
          System.out.println(foundName); 
      } 
      else { 
          System.out.println("No name found"); 
      } 
    }
````
To:
````agsl
public static void pickName( 
    final List<String> names, final String startingLetter) {
        final Optional<String> foundName = names.stream()   
            .filter(name->name.startsWith(startingLetter)) 
            .findFirst(); 
        foundName.ifPresent(name-> System.out.println("Hello " + name));
````
- Again, we are using ``stream()`` and intermediate opertaion ``filter()`` to found the name.
- ``findFirst()`` terminal opertaion will return the first element that returned true on the lamba expression passed in the ``filter()`` operation.
  - It returns an Optional which gives protection to NullPointerException. Can have a value or be null.
- Using the method ``ifPresent()`` we can point out the action to be done, print, if the Optional returned does contain a non null value.

### Reducing a Collection

All operations above had ended up by giving one unique element. It's time to manipulate elements against each others.

In this example:
````
friends.stream() 
  .mapToInt(name-> name.length()) 
  .sum());
 ````
- We leveraged the ``mapToInt()`` method, a variation of the map operation (variations like ``mapToInt()``, ``mapToDouble()``, and so on create type-specialized steams such as ``IntStream`` and ``DoubleStream``)
- Reduced the resulting lenght with the ``sum()`` terminal operation.
- We could use a variety of methods like ``max()`` to find the longest length, ``min()`` to find the shortest length, ``sorted()`` to sort the lengths, ``average()`` to find the average of the length, and so on.
-  map-reduce pattern

We can use the ``reduce()`` method to compare two elements against each other and pass along the result for further comparison with the remaining elements in the collection.

````agsl
final Optional<String> aLongName = friends.stream()
    .reduce((name1, name2)-> name1.length() >= name2.length() ? name1 : name2); 
aLongName.ifPresent(name-> System.out.println(String.format("A longest name: %s", name)));
````

- The result of the ``reduce()`` method is an Optional because the list on which ``reduce()`` is called may be empty. In that case, there would be no longest name. If the list had only one element, then ``reduce()`` would return that element and the lambda expression we pass would not be invoked.

If we want to set a default or a base value, we can pass that value as an extra parameter to an overloaded variation of the reduce() method.

````agsl
final String steveOrLonger = friends.stream() 
    .reduce("Steve", (name1, name2)-> name1.length() >= name2.length() ? name1 : name2);
````

### Joining elements

Under the hood the String’s join() method calls upon the StringJoiner to concatenate the values in the second argument, a varargs, into a larger string separated by the first argument.

````System.out.println(String.join(", ", friends));````

## Strings, Comparators and Filters

### Iterating a String

- ``chars()`` is a method that returns a Stream with all the characters of the string so we can iterate.

### Implementing the Comparator Interface

Imagine the following code:
````
List<Person> ascendingAge = people.stream() 
    .sorted((person1, person2)-> person1.ageDifference(person2)) 
    .collect(toList()); 
printPeople("Sorted in ascending order of age: ", ascendingAge);
`````

We first transformed the given List of people to a Stream using the stream() method. We then invoked the sorted() method on it. This method takes a Comparator as its parameter. Since Comparator is a functional interface, we conveniently passed in a lambda expression. In this case, the lamba expression calls a comparing method. Finally, the terminal opertaion collect will reduce the list into a diserable format. A list in this case.

Comparator’s compareTo()abstract method takes two parameters, the objects to be compared, and returns an int result. To comply with this, our lambda expression takes two parameters.

We can method-referece ``ageDifference()`` method even:
```
people.stream() 
    .sorted(Person::ageDifference)
`````
And avoid indicating the two parameters.

If we want to order it on opposite order we can use ``revsersed()`` method.
```
Comparator<Person> compareAscending = (person1, person2)-> person1.ageDifference(person2); 
Comparator<Person> compareDescending = compareAscending.reversed();
```

Comparators, however, can be not only added to sorting methods. We can pass them as parameters of a ``min()`` or ``max()`` or ``avg()`` method as well to find the desired value:

```
people.stream() 
    .min(Person::ageDifference) 
    .ifPresent(youngest-> System.out.println("Youngest: " + youngest));
```

### Multiple and Fluent Comparisons

Althought the code is much better this way we can even improve it more with convenience functions such as ``comparing()``:
```
final Function<Person, Integer> byAge = person-> person.getAge(); 
final Function<Person, String> byTheirName = person-> person.getName(); 

printPeople("Sorted in ascending order of age and name: ", 
people.stream() 
        .sorted(comparing(byAge)
        .thenComparing(byTheirName))
    .collect(toList()));
```

In the code we statically imported the comparing() method in the Comparator interface. The comparing() method uses the logic embedded in the provided lambda expression to create a Comparator. In other words, it’s a higher-order function that takes in one function (Function) and returns another (Comparator).

So we created a couple of lambda expressions, one to return the age of a given person and the other to return its name. Comparing does his maggic based on those expressions.

### Using the collect Method and the Collectors Class

The convenience of this concise version of ``collect()`` along with the Collectors utility doesn’t stop here. There are several methods on the Collectors to perform various collect or accumulate operations. For example, in addition to ``toList()``, there is ``toSet()`` to accumulate into a set, ``toMap()`` to gather into a key-value collection, and ``joining()`` to concatenate the elements into a string. We can also join multiple combine operations using methods like ``mapping()``, ``collectingAndThen()``, ``minBy()``, ``maxBy()``, and ``groupingBy()``.

```
Map<Integer, List<Person>> peopleByAge = people.stream()
    .collect(Collectors.groupingBy(Person::getAge));

 System.out.println("People grouped by age: " + peopleByAge);
```
We can play a lot:
```
Comparator<Person> byAge = Comparator.comparing(Person::getAge); Map<Character, Optional<Person>> oldestPersonInEachAlphabet = 
people.stream() 
    .collect(groupingBy(person-> person.getName().charAt(0), reducing(BinaryOperator.maxBy(byAge)))); 
    
System.out.println("Oldest person in each alphabet:"); 
System.out.println(oldestPersonInEachAlphabet);
```
We first group the names based on their first letter. For this, we pass a lambda expression as the first parameter to the ``groupingBy()`` method. From within this lambda expression we return the first character of the name for grouping purposes. The second parameter in this example, instead of mapping, performs a reduce operation. In each group, it reduces the elements to the oldest person, as decided by the ``maxBy()`` method. The syntax is a bit dense due to the combination of operations, but it reads like this: group by first character of name and reduce to the person with maximum age.

### Listing All Files in a Directory

- Getting all files in a directory
```
Files.list(Paths.get(".")) 
    .forEach(System.out::println);
```
- Getting all directories in a directory
```
Files.list(Paths.get(".")) 
    .filter(Files::isDirectory)
    .forEach(System.out::println);
```
- Getting all files in a directory by file name
```
Files.newDirectoryStream( Paths.get("fpij"), path-> path.toString().endsWith(".java")) .forEach(System.out::println);
```
- Getting all files in a directory based o file properties
```
final File[] files = new File(".").listFiles(file-> file.isHidden());
```
## Designing with lambda expressions
