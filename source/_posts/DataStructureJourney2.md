---
title: Data Structure Review#2 - Stack
date: 2019-07-27 15:55:14
tags: 
- Data Structure
categories:
- Data Structure
img: /medias/datastructure/data-structure.jpg
summary: Once you try to program efficiently, you can't prevent data structure from being involved. To help myself review data structure, I started a whole new chapter to talk about it.
---

## What Is A Stack

Stack is a list-like structure in which elements maybe inserted or removed from only one end. It seems that this makes it less practical and less flexible than the lists, but it also makes it easy to implement and more efficient. We call this feature "LIFO" which stands for "Last-In, First-Out". Simply, the insertion order goes reverse to that of removal.

---

## Stack ADT

Before we define a stack ADT, we have to make something clear. There is only one element in the stack accessible, that is the one on the top of the stack, which we call the `top` element. The insertion operation on a stack is called `push`. The removal operation is called `pop`. And it is worth noticing that we can just pop out the top element.

``` cpp

template <typename E> class Stack {
public:
    // Reinitialize the stack
    virtual void Clear() = 0;

    // Push an element onto the stack
    virtual void Push(const E& element) = 0;

    // Pop out the top element
    virtual E Pop() = 0;

    // Return the top value
    virtual const E& TopValue() const = 0;

    // Return the stack size
    virtual int Length() const = 0;
};

```

---

## Stack Implementation

### Array-based Stack Implementation

``` cpp

template <typename E>
class ArrayStack: public Stack<E> {
private:
    int maxSize;  // Maximum number of elements the stack can store
    int top;  // Index for the top element
    E* listArray;  // Array holding the element

public:
    ArrayStack(int size = defaultSize) {
        maxSize = defaultSize;
        top = 0;
        listArray = new E[maxSize];
    }

    ~ArrayStack() {
        delete [] listArray;
    }

    void Clear() {
        delete [] listArray;
        top = 0;
        listArray = new E[maxSize];
    }

    void Push(const E& element) {
        Assert((top != maxSize), "Stack is full!");
        listArray[top++] = element;
    }

    E Pop() {
        Assert((top != 0), "Stack is empty!");
        return listArray[--top];
    }

    const E& TopValue() const {
        return listArray[top - 1];
    }

    int Length() {
        return top;
    }
};

```

### Linked Stack Implementation

``` cpp

template <typename E>
class LinkedStack: public Stack<E> {
private:
    Node<E>* top;  // Pointer to the top element
    int size;  // The number of element in the stack

public:
    LinkedStack() {
        size = 0;
        top = NULL;
    }

    ~LinkedStack() {
        Clear();
    }

    void Clear() {
        while(top != NULL) {
            Node<E>* node = top;
            top = top->next;
            delete node;
        }
        size = 0;
    }

    void Push(const E& element) {
        top = new Node<E>(element, top);
        size++;
    }

    E Pop() {
        Assert((top != NULL), "Stack is empty!");
        Node<E>* node = top;
        E element = node->element;
        top = node->next;
        delete node;
        size--;
        return element;
    }

    const E& TopValue() {
        Assert((top != NULL), "Stack is empty!");
        return top->element;
    }

    int Length() {
        return size;
    }
};

```

>Notice:
>1. Here is a very important difference we have to notice between these two implementations. That is the top element in a array-based stack is at the end of the array, while the top element in a linked stack is at the very beginning, although they are both called top element. This kind of design make it easier to implement pushing and popping for both.
>2. The `Node` class has been defined last time. Check {% post_link DataStructureJourney1 here %}!

---

## Conclusion

All operations for the two implementations above both take constant time, so as for time aspect, they are equally efficient. The comparison of the total space requried is similar to the lists implementation: the array-based has to be predetermined and fixed, and the linked can flexibly adjust size but requires extra space for pointers.

---

## Reference

[1] Data Structure & Algorithm Analysis in C++ - Clifford A. Shafter
