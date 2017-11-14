
The blog briefly summarizes the components of the STL, some important common language features and new language features released with the new ISO standard C++17.

# STL

STL stands for Standard Template Library and contains implementations of data containers based on templates, associated access mechanisms and algorithms. It was designed by Alexander Stepanov and Meng Lee with the support of HP and was presented to the standardization committee for C++ at the end of 1993. In 1994 it was adopted as part of the C++ standard library.

The STL can be divided into four components:

## 1. Containers
The fundamental purpose of a container is to store multiple objects in a single container object. Different kinds of containers have different characteristics: speed, size, and ease of use. The choice of container depends on the characteristics and behavior you require.

Die Container können in zwei Arten unterschieden werden:
- Standard Container: deque, list, vector, map, set

- Container Adapters

An adapter is a class template that uses a container for storage and provides a restricted interface when compared    with the standard containers. Adapters do not have iterators, so they cannot be used with the standard algorithms. The standard adapters are: priority_queue, queue, stack

# 2. Iterators

An iterator is any object that, pointing to some element in a range of elements (such as an array or a container) and has the ability to iterate through the elements of that range using a set of operators. The most obvious form of an iterator is a pointer: A pointer can point to elements in an array, and can iterate through them using the increment operator (++).  Iterators generalize this concept so the same operators have the same behavior for any container, even trees and lists. Notice that while a pointer is a form of iterator, not all iterators have the same functionality of pointers. Depending on the properties supported by iterators, they are classified into five different categories:

**Input**

An InputIterator is an Iterator that can read from the pointed-to element. The increment operator (++) advances to the next element, but there is no decrement operator (--). Use the dereference operator (*) to read elements. You cannot read a single element more than once, and you cannot modify elements.
	
**Output**

With an OutputIterator you can wright to the pointed-to element. The increment operator (++) advances to the next element, but there is no decrement operator. Use the dereference operator (*) only to assign a value to an element. You cannot assign a value more than once to a single element. Unlike other iterator categories, you cannot compare output iterators.
	
**Forward**

ForwardIterators permit unidirectional access to a sequence. Use the increment operator (++) to advance the iterator and the dereference operator (*) to read or write an element. You can refer to and assign to an item as many times as you want. You can use a forward iterator anywhere an input or output iterator is required.
	
**Bidirectional**

A BidirectionalIterator is a ForwardIterator that can be moved in both directions (i.e. incremented and decremented).
	
**Random Access**

A RandomAccessIterator is a BidirectionalIterator that can be moved to point to any element in constant time. It also supports the [] operator to access any index in the sequence. Also, you can add or subtract an integer to move a random access iterator by more than one position at a time. Subtracting two random access iterators yields an integer distance between them. Thus, a random access iterator is most like a conventional pointer, and a pointer can be used as a random access iterator.

Each container offers one of the above mentioned iterators, which depends on the access properties of the container. The following table shows the containers and the iterators they offer.

| Container | Iterator               |
|-----------|------------------------|
| vector    | RandomAccessIterator   | 
| deque     | RandomAccessIterator   |
| list      | BidirectionalIterator  |
| map       | BidirectionalIterator  |
| set       | BidirectionalIterator  |

<font color="red">Note:</font> The most important point to remember about iterators is that they are potentially unsafe. Like pointers, an iterator can point to a container that has been destroyed or to an element that has been erased. You can advance an iterator past the end of the container in the same way a pointer can point past the end of an array. With a little care and caution, however, iterators are safe to use.

## 4. Algorithms

The algorithms library defines functions for a variety of purposes (e.g. searching, sorting, counting, manipulating) that operate on ranges of elements. Algorithms work with iterators, and therefore with almost any container. The algorithms require a special category of an iterator. This category is the minimal functionality needed, so you can, for example, use a random access iterator where at least a forward iterator is needed. The algorithms are not considered in this blog. For more information see: http://en.cppreference.com/w/cpp/algorithm


## 3. Function Objects

Function objects (also known as functors) are an STL feature that you may not be able to use immediately when you start using the STL. However, they are very useful in many situations and an STL facility that you should get to know. They give the STL a flexibility that it would not otherwise have and also contribute to the efficiency of the STL. The most common uses for function objects are generating data, testing data, and applying operations to data.

A function object is simply any object of a class that provides at least one definition for operator (). This means that if you declare an object f of the class in which this operator () is defined, you can use this object f like an "ordinary" function. Following example shows how to create and use function objects:

```c++
class Print{
    int n;
    public:
    Print() :n(0) {}
    void operator()(int v)
    {
        std::cout << v << " ";
        n++;
    }
    int Count() { return n;}
};
```
```c++
    int values[] = {1,2,3,4,5};
    std::vector<int> v (values, values + 5);
    Print p = for_each(v.begin(),v.end(), Print()); //1 2 3 4 5
    std::cout << "\nCount of digits: " << std::to_string(p.Count());    
```

The class Print contains the function operator (). A local variable n has also been defined. The value of the variable is used in the function and will be incremented. There is also a second function Count defined, which returns the value n. 

The call to the constructor of Print creates an instance of this class. That instance is passed as a function to for_each() and not as a pointer to a function. Therefore calls to Print inside for_each() can be inlined and run more efficiently. Another advantage of using function objects is that local variables can be used, which allows a higher flexibility. This is not possible when using pointers to a function.

Binary functors that return a boolean value are called binary predicates or comparitors or comparison functions. Unary functors that return a boolean value are called unary predicates. Either variety of functor that returns a boolean value may be referred to simply as a predicate function.

# Common C++ Features


