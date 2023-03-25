---
layout: post
title: "Dynamic array in C"
date: 2015-10-16
published: true
tags: c
---

Although I have been developing in C for while I had never heard about dynamic arrays. What is it? It is a feature which has been added as part of C99 standard. 

Dynamic array allows you to create an 'static' array on stack and calculate its size during runtime. Let's show it in example. 

```c
void
function1(char * filename) 
{
    char holder[strlen(filename) + 1];
    
    strcpy(holder, filename);
    printf("%s == %s\n", filename, holder);
}
```

You can see that holder's size is taken from strlen() of filename which is of course calculated during runtime. The variable holder is allocated on a stack and freed automatically once you returned from the function. It has very similar behavior like [alloca(3)](http://man7.org/linux/man-pages/man3/alloca.3.html) function form standard library. 

However difference between alloca() and dynamic arrays is result of sizeof. The result is size of pointer in case of alloca() and length of the array in case of dynamic array. Let's have a look on one more example.

```c
void
function2() 
{
    char holder1[] = "Hello";
    char holder2[strlen(holder1) + 1];
    char * holder3 = alloca(strlen(holder1) + 1);
    
    strcpy(holder2, holder1);
    strcpy(holder3, holder1);
    
    printf("strlen: %ld == %ld == %ld\n", 
        strlen(holder1), strlen(holder2), strlen(holder3));
        
    printf("sizeof: %ld == %ld != %ld\n",      
        sizeof(holder1), sizeof(holder2), sizeof(holder3));
}
```

As you can probably guess the result is going to be following.

```bash
strlen: 5 == 5 == 5
sizeof: 6 == 6 != 8
```

If you decide to use dynamic arrays you should be aware of one think and that is stack size. Most modern linux platforms limit their stack size to 8MB.

More information about the feature can be found at official [GCC](https://gcc.gnu.org/onlinedocs/gcc/Variable-Length.html) documentation. 

