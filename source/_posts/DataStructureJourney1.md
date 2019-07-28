---
title: Data Structure Review#1 - List
date: 2019-07-26 16:27:16
tags: 
- Data Structure
categories:
- Data Structure
img: /medias/datastructure/data-structure.jpg
summary: Once you try to program efficiently, you can't prevent data structure from being involved. To help myself review data structure, I started a whole new chapter to talk about it.
---

## What Is A List

List is a finite, ordered sequence of data items. It mathematically resembles set. Generally, all the data items in a list have the same type, but we do have some cases where different types of data items are in the same list.

>**Notice:**
>The "ordered" here doesn't mean that the list is sorted by value, it just means that every element in the list has a particular position instead.

When a list contains no elements, we call it an `empty` list. The number of elements that are currently stored in the list is called `length` of the list. The beginning of the list is called `head`, while the end of the list is called `tail`.

---

## List Operations

A list without any operations related to it is meaningless. Before we define a data structure implementation, we should first design some list operations. 

Obviously, a list should be capable of extending and shrinking. For extending, we can simply append an element at the end of the list or insert an element at a particular position. For shrinking, we should remove an element and shift all the elements behind to the head direction. Additionally, we should be able to get access to all the elements in the list for the purpose of retrieving. Although these are just some basic operations, you are free to add some more to make your data structure versatile.

### List ADT

ADT is short for `abstract data type`. It is a realization of a data type as a software component. It contains the logic of data storing and some operations on the data. However, it is just an interface, which is separated from its corresponding implementation, making it possible for users to apply it simply without being stuck on the detail.

``` cpp

template <typename E> class List {

public:
    // Default constructor
    List() {}

    // Destructor
    virtual ~List() {}

    // Clear all list contents
    virtual void Clear() = 0;

    // Insert an element at the current position
    virtual void Insert(const E& element) = 0;

    // Append an element at the end of the list
    virtual void Append(const E& element) = 0;

    // Remove and return the current element
    virtual E Remove() = 0;

    // Set current position to the start of the list
    virtual void MoveToHead() = 0;

    // Set current position to the end of the list
    virtual void MoveToTail() = 0;

    // Move the current position to the next
    virtual void Next() = 0;

    // Move the current position one step left
    virtual void Prev() = 0;

    // Return the number of the elements
    virtual int Length() = 0;

    // Return the current position
    virtual int CurrPos() = 0;

    // Move to a particular position
    virtual void MoveToPos(int pos) = 0;

    // Return the element
    virtual const E& GetValue() const = 0;
};

```

---

## List Implementation

### Array-based List

``` cpp

template <typename E>
class ArrayList: public List<E> {

private:
    int maxSize;  // Maximum number of elements this list can store
    int listSize;  // Number of elements this list currently stores
    int curr;  // Current position
    E* listArray;  // Array holding the elements

public:
    ArrayList(int size = defaultSize) { // "defaultSize" can be any nonnegative integer
        maxSize = size;
        listSize = curr = 0;
        listArray = new E[maxSize];
    }
    
    ~ArrayList() {
        delete [] listArray;
    }

    void Clear() {
        delete [] listArray;
        listSize = curr = 0;
        listArray = new E[maxSize];
    }

    void Insert(const E& element) {
        Assert(listSize < maxSize, "The list is full!");
        for(int i = listSize; i > curr; i--) {
            listArray[i] = listArray[i - 1];
        }
        listArray[currPos] = element;
        listSize++;
    }

    void Append(const E& element) {
        Assert(listSize < maxSize, "The list is full!");
        listArray[listSize] = element;
        listSize++;
    }

    E Remove() {
        Assert((curr >= 0) && (curr < listSize), "Element doesn't exist!");
        E tempValue = listArray[curr];
        for(int i = curr; i < listSize - 1; i++) {
            listArray[i] = listArray[i + 1];
        }
        listSize--;
        return tempValue;
    }

    void MoveToHead() {
        curr = 0;
    }

    void MoveToTail() {
        if(listSize == 0) {
            curr = 0;
        }
        else {
            curr = listSize - 1;
        }
    }

    void Next() {
        if(curr < listSize - 1)
            curr++;
    }

    void Prev() {
        if(curr > 0)
            curr--;
    }

    int Length() {
        return listSize;
    }

    int currPos() {
        return curr;
    }

    void MoveToPos(int pos) {
        Assert((pos >= 0) && (pos < listSize), "Invalid Position!");
        curr = pos;
    }

    const E& GetValue() const {
        return listArray[curr];
    }
};

```

### Linked List

List node implementation:

``` cpp

template <typename E> class Node{
public:
    E element;
    Node* next;

    Node(const E& elementValue, Node* nextNode = NULL) {
        element = elementValue;
        next = nextNode;
    }

    Node(Node* nextNode = NULL) {
        next = nextNode;
    }
};

```

Linked list implementation:

``` cpp

template <typename E>
class LinkedList: public List<E> {
private:
    Node<E>* head;  // Head of the list
    Node<E>* tail;  // Tail of the list
    Node<E>* curr;  // Current node of the list
    int size;  // Number of the nodes in the list

    // Initializtion
    void Initialization() {
        curr = tail = head = new Node<E>;
    }

    // Release store
    void Free() {
        while(head != NULL) {
            curr = head;
            head = head->next;
            delete curr;
        }
    }

public:
    LinkedList() {
        Initialization();
    }

    ~LinkedList() {
        Free();
    }

    // Clear the contents in the list
    void CLear() {
        Free();
        Initialization();
    }

    // Insert an element at current position
    void Insert(const E& element) {
        curr->next = new Node<E>(element, curr->next);
        if(curr == tail) {
            tail = curr->next;
        }
        size++;
    }

    // Append an element at the end of the list
    void Append(const E& element) {
        Node<E>* node = new Node<E>(element, NULL);
        tail->next = node;
        tail = node;
        size++;
    }

    // Remove and return current element
    E Remove() {
        Node<E>* temp = head;

        // If the current node is the head node
        if(temp == curr) {
            head = head->next;
            E element = head->element;
            delete temp;
            size--;
            return element;
        }
        else {
            // Find the node preceding the current node
            while(temp->next != curr) {
                temp = temp->next;
            }
            temp->next = curr->next;
            E element = curr->element;
            Node<E>* delTemp = curr;
            curr = temp->next;
            delete delTemp;
            size--;
            return element;
        }
    }

    // Move to the start
    void MoveToStart() {
        curr = head;
    }

    // Move to the end
    void MoveToEnd() {
        curr = tail;
    }

    // Move to the preceding position
    void Prev() {
        if(curr != head) {
            Node<E>* temp = head;
            while(temp->next != curr) {
                temp = temp->next;
            }
            curr = temp;
        }
    }

    // Move to the next position
    void Next() {
        if(curr != tail) {
            curr = curr->next;
        }
    }

    int Length() const {
        return size;
    }

    int CurrPos() const {
        int count = 1;
        Node<E>* temp = head;
        while(temp != curr) {
            count++;
            temp = temp->next;
        }
        return count;
    }

    // Move to a particular position
    void MoveTo(int pos) {
        int count = 1;
        Node<E>* temp = head;
        while(count != pos) {
            count++;
            temp = temp->next;
        }
        curr = temp;
    }

    E GetValue() const {
        return curr->element;
    }
};

```

---

## Conclusion

These two basic list implementations do have their own pros and cons respectively. For array-based list, it is obvious that the size of it has to be predetermined and it is fixed. While with linked list, you can dynamically adjust the size of the list by inserting or removing nodes, however, it requires more space since a node has to store a pointer to the next node, except for the element. As to the time cost, the array-based list is more time-consuming when it comes to the inserting and removing, because the list has to resort all the elements following, which is not neccessary in the linked list.

---

## Bye Bye

The first part of the data structure is finished. I think it is a very good start and a beneficial warm-up for me, whose purpose is to learn and to get more familiar with data structure. I hope you guys like it, and see you next time.

---

## Reference

[1] Data Structure & Algorithm Analysis in C++ - Clifford A. Shafter

