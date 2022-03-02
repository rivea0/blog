---
layout: post
title:  Custom endsWith and startsWith Functions
categories: [Python, JavaScript, C]
---

When working with strings, there might come a time that you might want to check if the string starts with or ends with another given string. Luckily, JavaScript and Python have their own built-in function to do the job, aptly named `startsWith()` & `endsWith()` in JavaScript, and `startswith()` & `endswith()` in Python. However, not to reinvent the wheel, but let's say we want to implement them our own way. Because, why not?

## Negative Indexing
One thing that might be helpful, before we start off, is the concept of negative indexing. For example, not in all languages, but the last character of a string can be accessed with the index number -1. The second to last character will be -2, and so on. Python allows the use of negative indexes for strings (and for most [iterables](https://docs.python.org/3.9/glossary.html#term-iterable){:target="_blank"}), and JavaScript's `slice` method also allows negative indexing. These will come in handy.

Python example:

```python
name = 'David'
name[-1] # d
name[-2] # i
```

We cannot access the character directly with negative indexes in JavaScript as it will return `undefined`, but we can use `slice`:

```javascript
let name = 'David';
name[-1] // undefined
name.slice(-1) // d
name.slice(-2) // id
```

## Implementing endsWith
Now, let's check if a string ends with another given string. Now that we know that negative indexes start from the end of the string, we can try something like this:

Python example:

```python
name = 'David'
target = 'vid'

name[-len(target):] == target # True
```

JavaScript example:

```js
let name = 'David';
let target = 'vid';

name.slice(-target.length) === target // true
```

We can take a look at what we did one by one, so that it's more clear. The first thing we see is that we get `target`'s length, which will be in our example's case, 3 (the length of `'vid'`). And, with negative indexing, we started from -3rd index of our original string and just compared the two. `name.slice(-target.length)` will start from the -3rd index of `name` up to the end of the string, which will be `'vid'` and voilà! — they're the same.

It is a nice, one-liner way to do it. Now let's try our hand at `startsWith`, which will be easier than this one.

## Implementing startsWith

We'll use the same components, slicing and using the target string's length. Let's do it.

Python example:

```python
name = 'David'
target = 'Dav'
name[:len(target)] == target # True
```

JavaScript example:

```js
let name = 'David';
let target = 'Dav';
name.slice(0, target.length) === target // true
```

Slicing the original string from the start to the length of the target string, gives us the string with the same length of `target`. So, `name.slice(0, target.length)` in this case, starts from the start of the string and goes up to the 3rd index (length of `'Dav'`). We only check if the two strings are the same, and that's it.

## Dissecting the Implementations
We have written great one-liners, and just implemented our own way to do `startsWith` and `endsWith`. Here are the functions (let's write the function names in [snake case](https://en.wikipedia.org/wiki/Snake_case){:target="_blank"} so as not to confuse ourselves with the built-in ones):

In Python:

```python
def starts_with(string, target):
    return string[:len(target)] == target
```
```python
def ends_with(string, target):
    return string[-len(target)] == target
```

In JavaScript:

```js
function starts_with(string, target) {
  return string.slice(0, target.length) === target;
}
```
```js
function ends_with(string, target) {
  return string.slice(-target.length) === target;
}
```

These are fine, but what about implementing the same logic another way?  Maybe, with another language? One that will help us think in lower-level.

What about this one?

```c
#include <stdio.h>
#include <stdbool.h>
#include <string.h>

bool starts_with(char *string, char *target) {
  int target_length = strlen(target);
  for (int i = 0; i < target_length; i++) {
    if (string[i] != target[i]) {
      return false;
	}
  }
  return true;
}
```

What we do in our C program is the same idea, only that we compare each character of our original string and the target string, up to the index which is `target`'s length; if any of them are not the same, we return false.
Here is the `ends_with`:

```c
#include <stdio.h>
#include <stdbool.h>
#include <string.h>

bool ends_with(char *string, char *target) {
  int target_length = strlen(target);
  int starting_index = strlen(string) - target_length;
  for (int i = 0; i < target_length; i++) {
    if (string[starting_index + i] != target[i]) {
      return false;
	}
  }
  return true;
}

```

We don't use negative indexing, of course, but the idea is the same. We use the `target`'s length for our starting index, so the length of our string minus the length of the target string (`strlen(string) - target.length`) is the number that we could use as our "negative" starting point. 

Here's the whole code in C:

```c
#include <stdio.h>
#include <stdbool.h>
#include <string.h>

// Function prototypes
bool starts_with(char *string, char *target);
bool ends_with(char *string, char *target);


int main(void) {
  char *str = "David";
  char *target_end = "vid";
  char *target_start = "D";

  // prints "true"
  printf("%s\n", starts_with(str, target_start) ? "true" : "false");

  // prints "true"
  printf("%s\n", ends_with(str, target_end) ? "true" : "false");

}

bool starts_with(char *string, char *target) {
  int target_length = strlen(target);
  for (int i = 0; i < target_length; i++) {
    if (string[i] != target[i]) {
      return false;
	}
  }
  return true;
}

bool ends_with(char *string, char *target) {
  int target_length = strlen(target);
  int starting_index = strlen(string) - target_length;
  for (int i = 0; i < target_length; i++) {
    if (string[starting_index + i] != target[i]) {
      return false;
	}
  }
  return true;
}

```

And now, time for some introspection.

Did we reinvent the wheel? Maybe.

Was it a problem that already been solved? That's what it was.

But, have we had some fun along the way? Well, depends on you, but I certainly did.
