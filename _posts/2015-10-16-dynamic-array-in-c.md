---
layout: post
title: "Dynamic array in C"
date: 2015-10-15
published: false
tags: c
---

Although I have been developing in C for while I had never heard about dynamic arrays. What is it? Well by my opinion pretty ugly thing. 

Dynamic array allows you to create a 'static' array on a stack of size calculated during runtime. Let's show it on example. 

{% highlight c %}
void
function1(char * filename) 
{
    char holder[strlen(filename) + 1];
    
    strcpy(holder, filename);
    printf("%s == %s\n", filename, holder);
}
{% endhighlight %}

We created .... holder size is ... holder implemented by alloca()

-sizeof example

{% highlight c %}
void
function2() 
{
    char holder1[] = "Hello";
    char holder2[strlen(holder1) + 1];
    char * holder3 = alloca(strlen(holder1) + 1);
    
    strcpy(holder2, holder1);
    strcpy(holder3, holder1);
    
    printf("strlen: %d == %d == %d\n", 
        strlen(holder1), strlen(holder2), strlen(holder3));
        
    printf("sizeof: %d == %d != %d\n",      
        sizeof(holder1), sizeof(holder2), sizeof(holder3));
}
{% endhighlight %}

-however difference because sizeof() is pointer..


-C99 

More details about the features can be found at https://gcc.gnu.org/onlinedocs/gcc/Variable-Length.html

