# Autocomplete

Autocomplete is pervasive in modern applications. As the user types, the program predicts the complete _query_ (typically a word or phrase) that the user intends to type. Autocomplete is most effective when there are a limited number of likely queries. For example, the Internet Movie Database uses it to display the names of movies as the user types; search engines use it to display suggestions as the user enters web search queries; cell phones use it to speed up text input.

In these examples, the application predicts how likely it is that the user is typing each query and presents to the user a list of the top-matching queries, in descending order of likelihood. These likelihoods are determined by historical data, such as box office revenue for movies, frequencies of search queries from other Google users, or the typing history of a cell phone user. For the purposes of this assignment, you will have access to a corpus of English text from which you will deduce how likely it is for certain words to be autocompleted in various ways.

The performance of autocomplete functionality is critical in many systems. For example, consider a search engine (such as Google) which runs an autocomplete application on a server farm. According to one study, the application has only about *50ms* to return a list of suggestions for it to be useful to the user. Moreover, in principle, it must perform this computation _for every keystroke typed into the search bar_ and _for every user_!

In this assignment, you will implement autocomplete in three phases.  First, you will take a corpus of text and count which words appear most frequently.  This will be done using a hash table of your creation.  Second, you will sort the words in descending order of frequency (so the most common words appear at the beginning of the list).  This will be done using the quicksort algorithm. Third, you will create a different hash table which associates every possible word prefix (a partial word that would be typed in by user) with the word that the prefix should autocomplete to.  For instance, the word prefix "th" might be autocompleted in various ways, but the most likely word is "the," because it appears more frequently in English text than other words that start with "th."

## Purpose
The purpose of this project is twofold: One, you will be learning how to implement a hash table and the quicksort algorithm.  Two, you will learn about some C++ classes that are useful in the real world, namely `list` (the C++ built-in doubly-linked list), and `unordered_map` (the C++ built-in hash table class).

## Getting started

Download the starter code and read through it.  Download the sample text files as well.  Here's what they mean:

 - `main.cpp`: Standard main file.  Lots of code is already written for you.  You will eventually be adding code here to implement quicksort and some miscellaneous code.
 - `Hashtable.h` and `Hashtable.cpp`: Code to implement a hash table that maps `string`s to `int`s.
 - `small.txt`: A small corpus of text that is completely made up.
 - `nyt.txt`: A large corpus of text that is taken from a collection of *New York Times* articles.

The flow of the program is as follows:

 - Your program will read a text file containing English words.  Each word will be counted and the frequencies will be maintained in a hash table (created by you) mapping each word to its frequency.
 - Your program will turn the hash table into a vector of words and their corresponding frequencies.  This is so we can then sort the vector by word frequency to find out which words are most commonly used in English.  
         - It's good to ask yourself why this step is needed.  Note that hash tables have no notion of "order" to them, so our hash table of word frequencies cannot be used directly to find the most frequent English words.  
         - Additionally, switching to a symbol table representation that preserves order among the keys (such as a binary search tree) would not help either in this situation, as such a symbol table would let us find the *words* that come alphabetically first or last, but would be helpful in finding the *frequencies* that are least or greatest.
 - After the vector has been sorted by descending word frequency, we will create a second hash table (using an `unordered_map`) that will map partial words to their autocompleted suggestions.

## Step 1: Implement the self-designed hash table
The `Hashtable.h` and `Hashtable.cpp` files implement a hash table that maps strings to integers.  The code is designed to be generic, referring only to "keys" (strings) and values (integers), but will be used in this program to store words and their frequencies.

This hash table uses separate chaining to store entries.  Specifically, each key-value pair is maintained in a `struct` called an `entry`.  The `Hashtable` class maintains a `vector` of linked lists of entries.  We use the C++ built-in `list` class which implements a doubly-linked list.  

I suggest the following steps to write this class:

- First, skip to the end of this document and read about the `list` class.  Then write these functions:
 - Write the `put()` function, which accepts a new key-value pair and adds it to the hash table.  Follow the instructions in the code.
 - Write the `get()` function, which searches the hash table for a specific key and returns the corresponding value.
 - Write some tests in `main()` to make sure these functions are working.  Comment the tests out when done.
 - Write the `contains()` function.  The logic should be very similar to `get()`.  Write tests in `main()`.
 - Write the `print()` and `numEntries()` functions.  Write tests in `main()`.

At this point, you should have a working hash table!

## Step 2: Implement reading the corpus text file and creating the hash table

 - Fill in the code in `main()` to read in the corpus text and keep a count of each word frequency.  
 - When done, compare your output on `small.txt` and `nyt.txt` against mine.  It should match up to printing the frequency table.

## Step 3: Implement quicksort

 - Write the `toVector()` function in `Hashtable.cpp`. 
 - Write the `printWordFreqVector()` function in `main()`.  You should be able to call this function now and check your output against mine.
 - Write the `partition()` and `quicksort()` functions in `main()`.
 - Test your output against mine.

## Step 4: Implement the construction of the autocomplete hash table.
- For this portion of the project, you will use the built-in C++ type `unordered_map` to store the autocomplete hash table (You write your own from scratch like in Step 1, but it's better to get practice using the C++ types).
        - Note that in the real world, you will rarely implement your own symbol table of any kind (BST or hash table) from scratch.  Always prefer the built-in types, as they have been proofread by many people and optimized for speed.
- Fill in the code in `main()` under "Step 4" for this.

## Step 5: Test your code 

- The code for step 5 is already written for you in `main()`; you should be able to type in words and you will see the autocomplete information for every possible prefix of the word.

## Testing and submitting

Test your output against mine for the `small.txt` and `nyt.txt` examples.

Upload `main.cpp`, `Hashtable.h`, and `Hashtable.cpp`.

## Hints, tips, and miscellaneous

- As usual, your output should match mine as closely as possible. We’re all using the same hash function so the order of everything should match 100%.
- If you get errors like this: `./Hashtable.h:22:33: error: a space is required between consecutive right angle brackets (use '> >')` 
that means you need to put spaces between the angle brackets. The reason for this is without the spaces, C++ thinks that two angle brackets together are one symbol (like in `cin >> x`). Older versions of C++ require this, newer versions don’t. Consider getting a newer version of your C++ compiler!
- The `get()` & `put()` functions in the hash table should **not** iterate over the entire table. The whole point of a hash table is you get constant time access to the correct slot in the table, calculated from the hash function. So the first thing you should do inside get & put is call hash on the key value; this will give you the slot in the table that you should be accessing.  You certainly may iterate over the list at that slot.
- On the other hand, `print()` is allowed to iterate over the table, b/c it needs to print the whole thing.

## The C++ `list` class

C++ has a built-in doubly-linked list class called (unsurprisingly) `list`.  You may use it by putting `#include <list>` at the top of your code.  We will be implementing a hash table using chaining, so we’ll use a table that stores linked lists of `entry`s.  The reference material is at [http://en.cppreference.com/w/cpp/container/list](http://en.cppreference.com/w/cpp/container/list), but I’ll give you the highlights.  To introduce what a list can do, we’ll simplify things and use a list of ints:

#### Common operations:
- Make a new, empty list:   `list<int> mylist;` <p>
- lists have `push_back()` and `push_front()`, just like C++ vectors do.  They do analogous things: `push_back()` is an appending operation that adds an item to the back/tail of the list), and `push_front()` is a prepend operation that adds an item to the front/head of the list).
```c++
mylist.push_back(5);  
mylist.push_back(7);  
mylist.push_front(3);  // mylist is now [3, 5, 7]  
```     

* Get the number of elements in a list: `mylist.size()`

- Iterating over a `list`: Because `list` is a doubly-linked list, we don’t have random access to individual elements, so we can’t use square brackets with indices to access elements.  Instead, we can use a for loop to iterate through the list:
```c++
for (int item : mylist) {  
  cout << item;  
}
```
  
This is a useful idiom that you will use frequently to iterate over a list.  However, what if we want to change items within the list as we’re iterating?  This won’t work:  
  
```c++
for (int item : mylist) {  
  item++;  
}
```
The issue is that `item` is a new variable that is a *copy* of the actual data from the internal linked list node.  However, C++ has a slick way of giving us access directly to the internal data of the list: we can use a _reference variable_:

```c++
for (int& item : mylist) {  
  item++;  
}
```
Notice the ampersand in the for loop.  This is similar to how pass-by-reference works in functions; it tells C++ that `item` should not be a *copy* of the internal integer from the linked list node, but rather it should refer directly to the contents of the list.  That way, when we do a `++` operation on `item`, we’re directly modifying the contents of the list, rather than a copy.  

Remember that your lists will contain entries, and you will be iterating over a `list` stored inside a vector, so you will want to do something like:

```c++
for (entry& p : table[some_index])
```

