
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

## 2. Iterators

An iterator is any object that, pointing to some element in a range of elements (such as an array or a container) and has the ability to iterate through the elements of that range using a set of operators. The most obvious form of an iterator is a pointer: A pointer can point to elements in an array, and can iterate through them using the increment operator (++).  Iterators generalize this concept so the same operators have the same behavior for any container, even trees and lists. Notice that while a pointer is a form of iterator, not all iterators have the same functionality of pointers. Depending on the properties supported by iterators, they are classified into five different categories:

**Input**

An InputIterator is an Iterator that can read from the pointed-to element. The increment operator (++) advances to the next element, but there is no decrement operator (--). Use the dereference operator (*) to read elements. You cannot read a single element more than once, and you cannot modify elements.
	
**Output**

With an OutputIterator you can wright to the pointed-to element. The increment operator (++) advances to the next element, but there is no decrement operator. Use the dereference operator (*) only to assign a value to an element. You cannot assign a value more than once to a single element. Unlike other iterator categories, you cannot compare output iterators.
	
**Forward**
ForwardIterators permit unidirectional access to a sequence. Use the increment operator (++) to advance the iterator and the 
dereference operator (*) to read or write an element. You can refer to and assign to an item as many times as you want. You can use a forward iterator anywhere an input or output iterator is required.
	
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

## Smart Pointers
In C++ there is no Garbage Collector, as know from Java. For this reason, C++ uses Smart Pointers to manage the resources. A smart pointer is a class that wraps a 'raw' C++ pointer, to manage the lifetime of the object being pointed to. It prevents most situations of memory leaks by making the memory deallocation automatic. More generally, smart pointers make object destruction automatic: an object controlled by a smart pointer is automatically destroyed (finalized and then deallocated) when the last (or only) owner of an object is destroyed, for example because the owner is a local variable, and execution leaves the variable's scope. Smart pointers also eliminate dangling pointers by postponing destruction until an object is no longer in use. C++ provides two different Smart Pointers.

**Unique Pointer**
A unique pointer takes over ownership of the object assigned to it. This means that two unique pointers can never refer to the same object. Note that if a pointer is passed to the unique pointer on an already existing object, the property of the object is transferred to the unique pointer and therefore the object must not be deleted using its original pointer.

```c++
    std::unique_ptr<int> p1 (new int(5));
    //std::unique_ptr<int> p2 = p1; //Compile error
    std::unique_ptr<int> p3 =std::move(p1);
    //Transfer ownership
    //p3 now owns the memory and p1 is set to nullptr
    std::unique_ptr<int> p4 (new int(7));
    
    std::cout << *p4; //7
    
    p3.reset(); //Destroys object, set to nullptr
    p1.reset(); //Does nothing
    
    p4.reset(new int(8));
    
    std::cout << *p4; //8
```

At the beginning a unique pointer (p1) is being created. It allocates one integer and initializes it with value 5. A second pointer is being generated and p1 is assigned to it. That is not possible, because an object can never be owned by two unique pointers. But you can move the ownership from p1 to a new unique pointer p3 with the function move. If an existing unique pointer (p4) is to refer to another new object, use the member function reset (new object) of unique pointer. reset () then takes over ownership of the new object and then destroys the old, previous object. If reset () is called without argument, the previous object will be deleted and the unique pointer will no longer contain an object reference.

**Shared Pointer**
A shared pointer is a container for a raw pointer. It maintains reference counting ownership of its contained pointer in cooperation with all copies of the shared pointer. An object referenced by the contained raw pointer will be destroyed when and only when all copies of the shared pointer have been destroyed.

```c++
    std::shared_ptr<int> p0 (new int(5));
    std::shared_ptr<int> p1 (new int(7));
    std::shared_ptr<int> p2 = p1; //Both now own the memory
    
    std::cout << p2.use_count(); // 2
    
    p1.reset(); //Memory still exists, due to p2
    
    std::cout << p2.use_count(); // 1
    
    p2.reset()
    //Destroyed the object, since no one else owns the memory
    
    std::cout << p2.use_count(); // 0
 ```
Two shared pointers are being created. Another shared pointer is being created which also refers to the same object as p1. If you then check the reference count it results 2. Afterwards reset p1 and the reference count result 1 because p2 still refers to the object. If you also reset p2 the object will be destroyed because no one else owns the memory.

There is also a third pointer called “weak pointer” in C++. A weak pointer is a container for a raw pointer. It is created as a copy of a shared pointer. The existence or destruction of weak pointer copies of a shared pointer have no effect on the shared pointer or its other copies. After all copies of a shared pointer have been destroyed, all weak pointer copies become empty. A weak pointer is mainly used when you need to access objects that may have been deleted in the meantime for some reason. While the shared pointer offers a powerful interface, the weak pointer is not considered as a smart pointer at all. Finally, it does not allow transparent access to the resource. Although it is possible to share a resource with it, the weak pointer cannot own it, because in fact he only borrows the resource from a shared pointer. It does not change the reference counter. 

For more information about smart pointers see: http://en.cppreference.com/w/cpp/memory

## C++17 Features

# Splicing for maps and sets

Question: How can we simple move elements from one map to another? 
In the old ISO Standard there was an opportunity to move elements from one map to another. You have to pick the element, put it into a new map and afterwards delete it from the old map. But the heap allocations and deallocations required to insert a new node and erase an old one are very expensive and can create an overhead. Yet another problem is that the key type of maps is const. This means that it cannot be changed. But how else can we move elements from a map to another?

The problem is solved with a new functionality released with C++17. 
There is a new function extract which unlinks the selected node from the container (performing the same balancing actions as erase). The extract function has the same overloads as the single parameter erase function: one that takes an iterator and one that takes a key type. They return an implementation-defined type which we refer to as the node handle. The node handle can be thought of as a special type of container which holds the node while in transit. It contains a copy of the allocator of the container. This is necessary so that the node handle can survive the container. Note that extracting a node naturally invalidates all iterators to it (since it is no longer an element of the container). Extracting a node from a map of any type invalidates pointers and references to it. This does not occur for sets.

There is also a new overload of insert that takes a node handle and inserts the node directly, without copying or moving it. Inserting a node into a map of any type invalidates all pointers and references to it. For sets this does not occur.

In C++17 there is as well a merge operation which takes a non-const reference to the container type and attempts to insert each node in the source container. Merging a container will remove from the source all the elements that can be inserted successfully, and (for containers where the insert may fail) leave the remaining elements in the source. This is very important—none of the operations we propose ever lose elements.

**Moving elements from one map to another**

We have moved elements of src into dst without any heap allocation or deallocation, and without constructing, destroying or losing any elements. The third insert failed, returning the usual insert return values and the orphaned node. 

```c++
    std::map<int, std::string> src;
    std::map<int, std::string> dst {{3,"three"}};
    
    src.emplace(1,"one");
    src.emplace(2,"two");
    src.emplace(3,"Digit 3");
    
    dst.insert(src.extract(src.find(1))); // Iterator version.
    dst.insert(src.extract(2)); // Key type version.
    auto r = dst.insert(src.extract(3)); // Key type version.
    
    // src == {}
    // dst == {“one”, “two”, “three”}
    // r.position == dst.begin() + 2
    // r.inserted == false
    // r.node == “Digit 3”
 ```
 
**Inserting an entire set**

The element “5” cannot be moved to the new set, since the same entry already exists. 
```c++
    std::set<int> src{1, 3, 5};
    std::set<int> dst{2, 4, 5};
    
    dst.merge(src);   // Merge src into dst.
    
    // src == {5}
    // dst == {1, 2, 3, 4, 5}
 ```
 
**Surviving the death of the container**

The node handle does not depend on the allocator instance in the container, so it is self- contained and can outlive the container. This makes possible things like very efficient factories for elements: 

 ```c++
auto new_record(){
    
    std::set<std::string> myset;
    
    myset.emblace("first");
    myset.emblace("second");
    
    return myset.extract(myset.begin());
}
myset.insert(new_record());
```
**Changing the key of a map element** 

This is a very useful operation that is not possible today without deleting the element and constructing a new one. While doing this with a node handle does require the insertion and tree balancing overhead, it does not cause any memory allocation or deallocation. 

```c++
    std::map<int, std::string> m;
    
    m.emplace(1,"bike");
    m.emplace(2,"car");
    m.emplace(3,"bus");
    
    auto nh = m.extract(2);
    nh.key() = 4;
    m.insert(move(nh));
    // m == {{1,”bike”}, {3,”bus”}, {4,”car”}}
```

