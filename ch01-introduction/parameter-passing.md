# Parameter Passing

In languages without manual memory management we usually don't care about how
the parameters we pass into a function are treated. What do I mean? Well, at
times we have to pass in variables that are large in size, have complex type,
or a combination of the two.

In Python, for instance, there is only one passing convention where objects are
passed by reference value. We are unable to specify whether a function gets its
own copy or a handle to the data being passed, and in Python it is always a
handle. If we want to isolate the data within the function scope we must
manually copy outside of the function. On the other hand, we by default get to
handle large complex data but are at risk of accidentally mutating said data.

However, C++ makes this distinction explicit. We are able to decide in the
function signature whether or not the data being passed gets copied, borrowed
with or without mutation, or transferred entirely. Because we are given a high
level of precision, we are able to write performant code when working with
large containers and or structures.

## Call-by-value

This is the most common passing convention used in languages like C and Java,
where the argument is copied into the formal parameter. However, like mentioned
before, we at times work with complex objects where copying the data may be an
inefficient operation or we want to manipulate the data being passed in.

Consider the following program:

```cpp
#include <iostream>

double average(double a, double b);

int main() {
    double x{1.0};
    double y{2.0};

    double z = average(x, y);

    return 0;
}
```

In this case, the function `average` takes in two values of type `double` which
are primitive types. Without analyzing the internals of the function, we expect
`average` to return a number without modifying the input parameters. However,
what if we introduce a new function,

```cpp
void swap(double a, double b);
```

where we expect the data in `a` to be swapped with the one found in `b`. But
because the arguments are being copied we don't actually have access to where
the data for both `a` and `b` live in memory, so we are unable to swap the
underlying assigned value between the two. Thus passing by value fails here.

## Call-by-reference (lvalue)

This convention, generally known as **call-by-reference**, passes a reference to
the original variable instead of a copy. Therefore, we are able to operate
directly on the caller's data rather than on a local copy. So we are allowed to
alter said data from within the function scope, if we wanted such behavior.

Going back to our `swap` function from before, we will change its function
signature in the following way,

```cpp
void swap(double &a, double &b);
```

Here, `a` and `b` are now aliases for whatever arguments are provided.

Let us consider a new function.

```cpp
std::string getRandomItem(std::vector<std::string> arr);
```

Hopefully, at this point, it should be apparent that we are not using the
correct passing convention. Why? A call by value would copy the argument
`arr` which could be of size 1, 100, 10000, etc. So this could potentially be a
expensive operation. How would we mitigate this? We would pass the argument
by reference. But now we must ask ourselves:
*Do we need to mutate the argument?*

## Call-by-constant-reference

This passing convention follows the same logic as a call by reference but it
restricts us from transforming the data of the argument within the function
scope. Why would this be useful? Looking back at the function `getRandomItem`,
the expected behavior of this function is to simply retrieve some element from
the vector being passed in. We do not expect the underlying data of the vector
to change in any way. So we would change its function signature like so

```cpp
std::string getRandomItem(const std::vector<std::string> &arr);
```

## Call-by-rvalue-reference

This is perhaps the most confusing passing convention in C++. To get a better
understanding of it we will have to take a slight detour and discuss reference
types to some detail.

### Lvalues & Rvalues

- **lvalue**: An expression that identifies a *non-temporary object*.
- **rvalue**: An expression that identifies a *temporary object* or is itself
a value that is not associated with an object.

For example consider the following code.

```cpp
std::vector<std::string> arr(3); // arr is an lvalue, 3 is an rvalue

const int x = 2; // x is an immutable lvalue, 2 is an rvalue

int y; // y is an lvalue

int z = x + y; // z is an lvalue, the expression (x + y) is an rvalue

std::string str = "foo"; // str is an lvalue, "foo" is an rvalue

std::vector<std::string> *ptr = &arr; // *ptr is an lvalue, &arr is an rvalue
```

Now, a **reference type** allows us to define a new name for an existing value.
As of C++11 we have two distinct types of references, **lvalue references** and
**rvalue references**.

# TODO: Continue explanation about reference types
