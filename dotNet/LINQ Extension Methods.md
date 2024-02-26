# LINQ Extension Methods

### What are Extension Methods?

Extension methods are a C# feature that allows developers to “add” new methods to existing types without modifying the original type’s code or creating a derived type. They are particularly useful when working with LINQ, as we can create custom query operators that seamlessly integrate with the existing LINQ syntax.

An extension method is defined as a static method within a static class, with the first parameter of the method having the `this` [modifier](https://www.bytehide.com/blog/access-modifiers-csharp) followed by the type being extended. Here’s an example of a simple extension method:

```cs
public static class StringExtensions
{
    public static string Reverse(this string input)
    {
        char[] chars = input.ToCharArray();
        Array.Reverse(chars);
        return new string(chars);
    }
}
```

In this example, we’ve created an extension method called `Reverse` that works on `string` objects. To use this extension method, we simply call it as if it were an instance method of the `string` class:

```cs
string name = "LINQ";
string reversedName = name.Reverse(); // Output: "QNIL"
```

## All Linq Entension Methods

We will use [Dumpify](https://www.nuget.org/packages/Dumpify) through out this. Which will give use `Dump()` extension method to show any object ouput on the console.

```bash
dotnet add package Dumpify --version 0.6.5
```

```cs
using Dumpify;

IEnumerable<int> collection = [1, 2, 3, 4, 5];
collection.Dump(); //Output: 1 2 3 4 5
```

Output:
![[Pasted image 20240224191059.png]]

## Filtering Method

### Where

This method will take a [[Csharp Action, Func, Predicate#3. **Predicate**|predicate]]. It goes through the predicate if it's true then it will be in the output, otherwise will not.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];
collection.Where(x => x > 2).Dump();
```

### OfType

It'll give us specific type of object.

```cs
IEnumerable<object> collection = [1, "one", 2, "two", 3, 4, 5];

collection.OfType<int>().Dump(); // output: 1 2 3 4 5
collection.OfType<string>().Dump();// output: "one" "two"
```

## Partitioning Method

### Skip

`Skip(3)` will skip the first 3 item from the list and return the rest of the item.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.Skip(3).Dump();
//Output: 4 5
```

### Take

`Take(3)` will return first 3 items

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];
collection.Take(3).Dump();
//Output: 1 2 3
```

### SkipLast

`SkipLast(3)` will skip last 3 items

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.SkipLast(3).Dump(); // Output: 1,2
```

### TakeLast

`TakeLast(3)` will take last 3 Items

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.TakeLast(3).Dump(); // Output: 3 4 5
```

### SkipWhile

`SkipWhile` will take a predicate and while condition is true it'll skip the item.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];
collection.SkipWhile(s => s < 4).Dump(); // Output: 4 5
```

### TakeWhile

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.TakeWhile(s => s < 4).Dump(); // Output: 1 2 3
```

### Problem in `TakeWhile` and `SkipWhile`

> [Problem With SkipWhile and TakeWhile](https://stackoverflow.com/questions/38986449/skipwhile-in-linq-is-not-working-as-excepted)
> This method tests each element of source by using predicate and skips the element if the result is true. **After the predicate function returns false for an element, that element and the remaining elements in source are yielded and there are no more invocations of predicate.**

This code will return nothing because first element return false that's why it assumes rest of the item will return false. _We can handle this using Where method._

```cs
IEnumerable<int> collection = [6, 1, 2, 3, 4, 5];
collection.TakeWhile(s => s < 4).Dump(); // Output:
```

## Projection Method

### Select

It give us take each of the value and project into different type.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5];

collection.Select((s, i) => $"index:{i} value:{s}").Dump();
```

Output:

```
 "index:0 value:1"
 "index:1 value:2"
 "index:2 value:3"
 "index:3 value:4"
 "index:4 value:5"
```

### SelectMany

This will allow us to iterate over multiple list like this:

```cs
IEnumerable<List<int>> collection = [[1, 2, 3], [4, 5]];
collection.SelectMany(s => s).Dump(); // Output: 1 2 3 4 5
```

If we iterate over the list like previous `Select` example. Second array index will start from 0.

```cs
IEnumerable<List<int>> collection = [[1, 2, 3], [4, 5]];
collection
    .SelectMany((s, j) => s
        .Select((p, i) => $"mainIndex:{j} subIndex:{i} value:{p}"))
    .Dump();
```

Output:

```
"mainIndex:0 subIndex:0 value:1"
"mainIndex:0 subIndex:1 value:2"
"mainIndex:0 subIndex:2 value:3"
"mainIndex:1 subIndex:0 value:4"
"mainIndex:1 subIndex:1 value:5"
```

### Cast

Allow us to cast one type to another type

```cs
IEnumerable<object> collection = [1, 2, 3, 4, 5];
collection.Cast<int>().Dump();
```

### Chunk

Divide collection into smaller chunk of multiple collection. `Chunk(3)` make one collection into two with each of 3 elements. It's opposite of `SelectMany` because we can always merge them again with `SelectMany`.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];

collection.Chunk(3).Dump(); // Output: [[1,2,3],[4,5,6]]
```

Output:
![[Pasted image 20240225005008.png]]

## Existence or Quantity Check

> **Immediate Execution** executes immediately where we write them.
> **Differed Execution** executes when we use them. _If immediate execution not written on side of the method then it's Differed execution._ >[Immediate and Differed Execution](https://youtu.be/7-P6Mxl5elg?t=474)

### Any (Immediate Execution)

It checks the existence of the element which passes the `predicate` it takes. Once it gets true in return, it will not check the rest of the item.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.Any(x => x > 2).Dump(); // Output: True
```

### All (Immediate Execution)

It checks for every single element of the list the predicate returns true or not.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.All(x => x > 2).Dump(); // Output: False
collection.All(x => x < 9).Dump(); // Output: True
```

### Contains (Immediate Execution)

Check for passed parameter exists in the list or not

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.Contains(2).Dump(); //Output: True
```

## Sequence manipulation

### Append and Prepend

`Append` will add the item at the end of the list, `Prepend` will add at the beginning of the list

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];

collection.Append(7).Dump(); // Output: 1 2 3 4 5 6 7
collection.Prepend(7).Dump(); // Output: 7 1 2 3 4 5 6
```

Output:
![[Pasted image 20240225011655.png]]

## Aggregation Methods

### Count (Immediate Execution)

Number of element count on the Collection. If it's called for an array it just simply call `Length` property but if its unknown then it iterates over the whole collection to get the count.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];

collection.Count().Dump(); //Output: 6
```

### TryGetNonEnumeratedCount (Immediate Execution)

If it could get the count without enumerating the whole collection, then it returns true, otherwise false.

```cs
var myEnumeration = new[] { 1, 2, 3, 4 };
var could = myEnumeration.TryGetNonEnumeratedCount(out var count);
```

We have an *Array* and an *Array* has a `Length` property the function could retrieve. So `could` is `true` and `count` is `4`. Internally `TryGetNonEnumeratedCount` calls `ICollection<T>.Count` if the type implements `ICollection<T>`. *Array* implements `ICollection<T>` so it's easy to get the count.

### Max (Immediate Execution)

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];

collection.Max().Dump(); //Output: 6
collection.Max(x => x * -1).Dump(); //Output: -1
```

First one iterate over the whole collection and return the max value
Second one multiply all of them with -1 and gets the max value which one is -1.

### MaxBy (Immediate Execution)

We redo previous Max example here. We will found output 1 because the condition will `x*-1` will be used to compare the value but after comparison it will return the underlying value.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];

collection.MaxBy(x => x * -1).Dump(); //Output: 1
```

A more commonly used example is this

```cs
IEnumerable<Person> people = [
    new Person ("Yasuda",11),
    new Person ("Sanda", 12)
];
people.MaxBy(x => x.Age).Dump(); // Output: Max Age object which is {"Sanda", 12}

record Person(string Name, int Age);
```

![[Pasted image 20240225025234.png]]

### Min (Immediate Execution)

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];

collection.Min(x => x * -1).Dump(); //Output: -6
```

### MinBy (Immediate Execution)

```cs
IEnumerable<Person> people = [
    new Person ("Yasuda",11),
    new Person ("Sanda", 12)
];
people.MinBy(x => x.Age).Dump();

record Person(string Name, int Age);
```

### Sum (Immediate Execution)

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.Sum().Dump(); //Output: 21
```

If we use type `object` here instead of `int` then the sum method will not work unless we convert it.

```cs
IEnumerable<object> collection = [1, 2, 3, 4, 5, 6];
collection.Sum(x => (int)x).Dump(); //Output: 21
```

### Average (Immediate Execution)

```cs
IEnumerable<object> collection = [1, 2, 3, 4, 5, 6];
collection.Average(x => (int)x).Dump(); //Output: 3.5
```

### LongCount (Immediate Execution)

Work same as count but return type is long.

```cs
IEnumerable<object> collection = [1, 2, 3, 4, 5, 6];
collection.LongCount().Dump(); //Output: 6
```

### Aggregate (Immediate Execution)

multiple overload of Aggregate method are available.

```cs
//overload 1
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.Aggregate((x, y) => x + y).Dump(); //Output: 21
```

- Here first it will sum 1 and 2. Then for the next iteration it will put the previous sum value in the `x` and add that with the next value `y` that is 3.

```cs
//overload 2
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.Aggregate(10, (x, y) => x + y).Dump(); //Output: 31
```

- Here first value of `x` will be 10 (aka seed value) and `y` will be 1 and rest of the procedure will remain same.

```cs
//overload 3
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
collection.Aggregate(9, (x, y) => x + y, x => x / 2).Dump(); //Output: 15
```

- Here, the last parameter will divide the value from the first two parameters(30) by 2.

## Element operator

### First and FirstOrDefault (Immediate Execution)

If the array is empty, the `First` method will throw an exception but `FirstOrDefault` will show the default value of that particular datatype. In `FirstOrDefault` we can specify the default value in the parameter.

```cs
IEnumerable<int> collection = [1, 2, 3, 4, 5, 6];
IEnumerable<int> collection2 = [];

collection.First().Dump(); //Output: 1
collection2.First().Dump(); //Output: System.InvalidOperationException: Sequence contains no elements

collection.FirstOrDefault().Dump(); //Output: 1
collection2.FirstOrDefault().Dump(); //Output: 0
collection2.FirstOrDefault(-5).Dump(); //Output: -5
```

### Single and SingleOrDefault (Immediate Execution)

`Single` expects only one value on the list otherwise it will through an exception. If the collection is empty, it'll also throw an exception.

```cs
IEnumerable<int> collection = [6];
IEnumerable<int> collection2 = [6, 4];
IEnumerable<int> collection3 = [];

collection.Single().Dump(); //Output: 6
collection2.Single().Dump(); //Output: System.InvalidOperationException: Sequence contains more than one element

collection3.Single().Dump(); //Output: System.InvalidOperationException: Sequence contains no elements
```

`SingleOrDefault` will also throw an exception if there is more than one value, but if the list is empty then it will return the default value.

```cs
IEnumerable<int> collection = [6];
IEnumerable<int> collection2 = [6, 4];
IEnumerable<int> collection3 = [];

collection.SingleOrDefault().Dump(); //Output: 6
collection2.SingleOrDefault().Dump(); //Output: System.InvalidOperationException: Sequence contains more than one element

collection3.SingleOrDefault().Dump(); //Output: 0
collection3.SingleOrDefault(-5).Dump(); //Output: -5
```

### Last and LastOrDefault (Immediate Execution)

Return last value. If empty `Last` throw and error but `LastOrDefault` return default value or value passed in the parameter.

```cs
IEnumerable<int> collection = [6, 7, 8, 9];
IEnumerable<int> collection2 = [];

collection.Last().Dump(); //Output: 9
collection2.Last().Dump(); //Output: System.InvalidOperationException: Sequence contains no elements

collection2.LastOrDefault().Dump(); //Output: 0
collection2.LastOrDefault(-6).Dump(); //Output: -6
```

### ElementAt and ElementAtOrDefault (Immediate Execution)

`ElementAt(1)` will return first index element. If the index is out of the range then it'll throw an error but `ElementAtOrDefault` will return default value of that type.

```cs
IEnumerable<int> collection = [6, 7, 8, 9];

collection.ElementAt(1).Dump(); //Output: 7
collection.ElementAt(5).Dump(); //Output: System.IndexOutOfRangeException: Index was outside the bounds of the array.

collection.ElementAtOrDefault(5).Dump(); //Output: 0
```

### DefaultIfEmpty

It will return default value if the collection is empty otherwise it'll do nothing.

```cs
IEnumerable<int> collection = [6, 7, 8, 9];
IEnumerable<int> collection2 = [];

collection.DefaultIfEmpty().Dump(); //Output: 6 7 8 9
collection2.DefaultIfEmpty().Dump(); //Output: 0
```

## Conversion Methods

### ToArray, ToList, ToDictionary, ToHashSet (Immediate Execution)

They convert the collection into an array, list, dictionary,HashSet or Lookup table.

```cs
IEnumerable<int> collection = [6, 7, 8, 9];

collection.ToArray().Dump(); //Output: int array
collection.ToList().Dump(); //Output: int List
collection.ToDictionary(key => key, value => value).Dump(); //Output: Dictionary with same value and key
collection.ToHashSet().Dump(); //Output: HashSet
```

Output:
![[Pasted image 20240225150916.png]]
![[Pasted image 20240225151053.png]]

### ToLookup (Immediate Execution)

It'll give a lookup table based on the parameter

```cs
IEnumerable<Person> peoples = [
    new("Abid", 16),
    new("Khabib", 19),
    new("Zaman", 16),
];

peoples.ToLookup(x => x.Age).Dump(); //Output : Two group of Object based on Age
peoples.ToLookup(x => x.Age)[19].Single().Name.Dump(); // Output: "Khabib"

record Person(string Name, int Age);
```

Output:
![[Pasted image 20240225152138.png]]

## Generation Methods

### AsEnumerable and AsQueryable

This two simply cast the list into `IEnumerable` or `IQueryable`

```cs
List<Person> peoples = [
    new("Abid", 16),
    new("Khabib", 19),
    new("Zaman", 16),
];

IEnumerable<Person> ppls = peoples.AsEnumerable();
IQueryable<Person> ppes = peoples.AsQueryable();

record Person(string Name, int Age);
```

### Range (Immediate Execution)

Give a range of items. `Range(9, 5)` here 9 is the starting value and 5 is the number of elements from 9.

```cs
IEnumerable<int> nums = Enumerable.Range(9, 5);
nums.Dump(); //output: 9 10 11 12 13
```

### Repeat (Immediate Execution)

`Repeat(9, 5)` this will repeat 9 element 5 times.

```cs
IEnumerable<int> nums = Enumerable.Repeat(9, 5);
nums.Dump(); //output: 9 9 9 9 9
```

### Empty (Immediate Execution)

`Empty<int>()` It will create an Empty int list. _It's a static method, so if we use this multiple times in our application then it will not create a new list every time but will reference the same static list of the Enumerable class._

```cs
IEnumerable<int> nums = Enumerable.Empty<int>();
nums.Dump(); //output: []
```

## Set Operations

### Distinct and DistinctBy

Distinct will get each unique value from the collection.

```cs
IEnumerable<int> nums = [1, 2, 3, 1, 2];

nums.Distinct().Dump(); //output: 1 2 3

IEnumerable<Person> people = [
    new(1, "Anas"),
    new (2, "Jahur"),
    new(3, "Jahur")
];
people.DistinctBy(x => x.Name).Dump(); // Output: it'll give only unique Name objects
record Person(int Id, string Name);
```

### Union, Intersect and Except

```cs
IEnumerable<int> nums = [1, 2, 3];
IEnumerable<int> nums2 = [2, 3, 4, 5];

nums.Union(nums2).Dump(); //output: 1 2 3 4 5
nums.Intersect(nums2).Dump(); //output: 2,3
nums.Except(nums2).Dump(); //output: 1
```

### UnionBy, IntersectBy and ExceptBy

```cs
IEnumerable<Person> peoples1 = [
    new(1, "Anas"),
    new (2, "Jahur"),
    new(3, "Kamil")
];
IEnumerable<Person> peoples2 = [
    new (2, "Jahur"),
    new(3, "Kamil"),
    new(4, "Hamid"),
];

peoples1.UnionBy(peoples2, x => x.Id).Dump(); //Output: Will return all person with Id 1 2 3 4
peoples1.IntersectBy(peoples2.Select(p => p.Id), x => x.Id).Dump(); //Output: will return objects with Id 2, 3
peoples1.ExceptBy(peoples2.Select(p => p.Id), x => x.Id).Dump(); //Output: will return objects with Id 1
record Person(int Id, string Name);
```

### SequenceEqual

Checks two collections are equal or not

```cs
IEnumerable<int> nums = [1, 2, 3];
IEnumerable<int> nums2 = [1, 2, 3];
IEnumerable<int> nums3 = [2, 3, 4, 5];

nums.SequenceEqual(nums2).Dump(); //output: True
nums.SequenceEqual(nums3).Dump(); //output: False
```

## Joining and Grouping methods

### Zip

this will pair up two or three collections. _If any of the collection length is smaller than another like one collection has 3 elements and another has 4 then output will be 3 pair of items._

```cs
IEnumerable<int> nums = [1, 2, 3];
IEnumerable<string> strings = ["a", "b", "c"];
IEnumerable<string> strings2 = ["b", "c"];

nums.Zip(strings).Dump(); //output: This will return tree tuples pairing with (1,"a"),(2,"b"),(3,"c")
nums.Zip(strings2).Dump(); //output: This will return tree tuples pairing with (1,"b"),(2,"c")
nums.Zip(strings, strings2).Dump(); //output: This will return tree tuples pairing with (1,"a","b"),(2,"b","c")
```

### Join

```cs
IEnumerable<Person> peoples = [
    new(1, "Anas"),
    new (2, "Jahur"),
    new(3, "Kamil")
];
IEnumerable<Product> products = [
    new (2, "Rice"),
    new(3, "Flour"),
];

peoples.Join(
    products,
    person => person.Id,
    product => product.PersonId,
    (prsn, prdct) => $"{prsn.Name} brought {prdct.ProductName}"
).Dump();
//Output
// "Jahur brought Rice"
// "Kamil brought Flour"
```

### GroupJoin

This is like a **LEFT OUTER JOIN in SQL**. You can use the Join clause to combine collections into a single collection.

```cs
IEnumerable<Person> peoples = [
    new(1, "Anas"),
    new (2, "Jahur"),
    new(3, "Kamil")
];
IEnumerable<Product> products = [
    new (2, "Rice"),
    new(3, "Flour"),
    new(2, "Flour"),
];

peoples.GroupJoin(
    products,
    person => person.Id,
    product => product.PersonId,
    (prsn, prdcts) => $"{prsn.Name} brought {string.Join(',', prdcts)}"
).Dump();

//Output:
// "Anas brought "
// "Jahur brought Product { PersonId = 2, ProductName = Rice },Product { PersonId = 2, ProductName = Flour }" │
// "Kamil brought Product { PersonId = 3, ProductName = Flour }"
```

### Concat

this will concat two collection into a single collection.

```cs
IEnumerable<int> collection1 = [1, 2, 3];
IEnumerable<int> collection2 = [3, 4, 5];

collection1.Concat(collection2).Dump(); //output: 1 2 3 3 4 5
```

### GroupBy

Group item by the given attribute.

```cs
IEnumerable<Person> peoples = [
    new(1, "Anas"),
    new (2, "Jahur"),
    new(3, "Jahur")
];

peoples.GroupBy(p => p.Name).Dump(); //Output: return the IEnumerable of two groups

var lastGroup = peoples.GroupBy(p => p.Name).Last().Dump();
lastGroup.Key.Dump(); //Output: Jahur

foreach (var p in lastGroup)
{
    p.Dump();
} //Output: return two object with "Jahur" name

record Person(int Id, string Name);
```

## Sorting

### Order, OrderBy, OrderDescending, OrderByDescending

```cs
IEnumerable<int> collection1 = [1, 3, 2];

collection1.Order().Dump(); //Output: 1 2 3
collection1.OrderBy(x => x).Dump(); //Output: 1 2 3
collection1.OrderDescending().Dump(); //Output: 3 2 1
collection1.OrderByDescending(x => x).Dump(); //Output: 3 2 1
```

### ThenBy, ThenByDescending

```cs
IEnumerable<Person> peoples = [
    new(1, "Anas"),
    new (2, "Jahur"),
    new(3, "Jahur")
];
peoples.OrderBy(x => x.Name).ThenByDescending(y => y.Id).Dump(); // Output: This will return object list 1 3 2 in this order
peoples.OrderBy(x => x.Name).ThenBy(y => y.Id).Dump(); // Output: This will return object list 1 2 3 in this order
```

### Reverse

```cs
IEnumerable<int> collection1 = [1, 2, 3];
collection1.Reverse().Dump(); //Output: 3 2 1
```

## Parallel LINQ (PLINQ)
