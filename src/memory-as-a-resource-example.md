Introduction example
--------------------

Let us give one example in different programming languages: creating an
array or a vector or something similar of numbers, and storing the sum of
the numbers in the first element of the vector.

This is how this looks like in Python:

```python
def compute_sum():
	numbers = [1, 2, 3]
	numbers[0] = numbers[0] + numbers[1] + numbers[2]
```

In Java or C#:

```java
void ComputeSum() {
	int[] numbers = {1, 2, 3};
	numbers[0] = numbers[0] + numbers[1] + numbers[2];
}
```

In Javascript:

```javascript
function ComputeSum() {
	var numbers = [1, 2, 3];
	numbers[0] = numbers[0] + numbers[1] + numbers[2];
}
```

If you more familiar with a programming language for which no example was
given, you can probably write down this simple function yourself.

On the first line in the function body, an array is created, containing the 
numbers one, two and three. 
When any of the examples above is is executed, under the hood, a part
of memory is reserved to store the numbers. The variable `numbers` contains
somehow a handle for the piece of memory that was reserved.

On the second line, the handle stored in `numbers` is used multiple times to
compute a simple sum and store it in the first element of the array.

If the general approach to using a resource was used, then there would be a
third line on which the resource manager was informed that we don't need the
memory for the array anymore. There is no line of code like that.

If memory was like any other resource in these programming languages, then you
would get in trouble when this function was called many, many times. Each and
every time, the function would reserve yet another piece of memory to store
its array. After a while, there would be no unused memory left and your
program would crash. In practice, there is a piece of your application that
prevents this from happening: the garbage collector.
