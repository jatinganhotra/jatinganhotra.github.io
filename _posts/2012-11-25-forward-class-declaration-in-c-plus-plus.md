---
layout: post
title: "Forward Class Declaration in C++"
date: 2012-11-25 21:00
# categories: [Forward Class Declaration, C++, Coding Tips]
tags: [Forward Class Declaration, C++, Coding Tips]
---

"In computer programming, a [forward declaration]("http://en.wikipedia.org/wiki/Forward_declaration) is a declaration of an identifier (denoting an entity such as a type, a variable, or a function) for which the programmer has not yet given a complete definition."

In C++, you should forward declare classes instead of including headers. Don't use an #include when a forward declaration would suffice.
When you include a header file you introduce a dependency that will cause your code to be recompiled whenever the header file changes. If your header file includes other header files, any change to those files will cause any code that includes your header to be recompiled. Therefore, you should prefer to minimize includes, particularly includes of header files in other header files.

You can significantly reduce the number of header files you need to include in your own header files by using forward declarations. For example, if your header file uses the File class in ways that do not require access to the declaration of the File class, your header file can just forward declare class File; instead of having to #include "file.h".

This will in turn speed a little bit the compilation.

**When can I use the forward class declaration?**  
Put yourself in the compiler's position: when you forward declare a type, all the compiler know is that this type exists; it knows nothing about its size, members or methods. This is why it's called an _incomplete type_. Therefore, you cannot use the _incomplete_ type at places where the compiler would need to know the layout of the type.

Assuming the following forward declaration
`class Forward;`

**You can use forward declarations in all the following cases, where**  

```cpp
class Master {
private:
    // Declare a member to be a pointer or a reference to the incomplete type
    Forward *ptr1;     
    Forward &ptr2;

public:

    // Declare functions or methods which accepts/return incomplete types:
    void ByValue(Forward by_value);
    Forward OrReturnValue();

    // Define functions or methods which accepts/return pointers/references 
    // to the incomplete type (but without using its members):
    void OrByPointer(Forward* by_pointer);
    void OrByReference(const Forward& by_reference);
    Forward& ReturnByRef();
    Forward* ReturnByPointer();

};
```

**You can't use forward declarations in all the following cases, where**  

```cpp
// Use it as a base class
class Master : Forward {} // compiler error!

// Use it to declare a member:
class Master {
    Forward cannot_define_obj_member; // compiler error!
};

// Define functions or methods using the incomplete type
void SomeFunc1(Forward x) {} // compiler error!
Forward    SomeFunc2()    {} // compiler error!

// Use its methods or fields, in fact trying to dereference a variable with incomplete type
class Master {
    Forward *forward_class_ptr;            
    void SomeFunc()            
    {
        forward_class_ptr->SomeMethod();      // compiler error!
        int i = forward_class_ptr->SomeField; // compiler error!
    }
};

// Whenever you use a class as a template parameter, the declaration of that class must be complete and not simply forward declared.
class Master {
      std::vector<Forward> some_stl_containers_;
};


// Delete the forward declared class in the module
class Forward;
class Master
{
    void SomeFunc(Forward* forward_class_ptr)
    {
        delete forward_class_ptr;
    }
};
// From the C++ standard: 5.3.5/5:
// "If the object being deleted has incomplete class type at the point of deletion and the complete class has a // non-trivial destructor or a deallocation function, the behavior is undefined."
// e.g gcc 4.2.1 errors out and reports a note: neither the destructor nor the class-specific operator delete will be called, even if they are declared when the class is defined.
```