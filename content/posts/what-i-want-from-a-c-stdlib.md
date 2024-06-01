---
title: "What I Want From a C stdlib"
date: 2024-06-01T00:26:01-05:00
---

> or what I always end up needing in C

Hi! I haven't blogged in a while but over this summer I plan to change that. I'll just be writing about random things on my mind. I've been writing more C lately so I think I'll do what is a rite of passage for systems programmers: complain about C and rant that my specific needs would be better met with yet another standard library replacement. So here goes...

# Motivation
There are several schools of thought when it comes to standard libraries (stdlibs). Python famously has the ["batteries included" philosophy](https://docs.python.org/3/tutorial/stdlib.html#batteries-included), where pretty much anything useful is shoved into the stdlib. C is on the opposite end where very little is included. This makes sense, both historically and when you consider C's goals. C needs to be portable. C can't make many assumptions about the underlying hardware. C needs to be simple (for a given value of simple).

When adding something to the standard library, the standards authors are understandably conservative. But because of this, the relative dearth of its stdlib, and the many reasons that it is still very desirable to write C, every C project of any considerable size reimplements many of the same things and many C programmers end up having their own little collection of libraries.

This is my attempt to codify what I would like from an initial version of my own sort of stdlib for my needs.

# Basic Collections
* Map
* Sorted Maps
* Dynamic Arrays

These are probably the biggest exclusions in my view. Pretty much every program I want to write ends up needing a hashtable or a dynamic array somewhere. And implementing a simple hashtable or growable array isn't even thaaat difficult but it's a pain.

A common argument against why a hashtable shouldn't be included in the standard library is that there is no "one size fits all" hashtable. When you create a custom data structure for your problem, you are able to exploit information about your problem domain that library authors simply do not have access to. But this is not a terribly convincing argument to me. Why not provide a hashtable which has perfectly reasonable performance? There is no such thing has a "one size fits all" memory allocator and malloc seems to do okay. And just like the case of malloc, when the hashtable implementation actually becomes a bottleneck, *then* you go and write a custom one.

There is one very interesting design tradeoff when writing a generic container type in C: how do you actually make it generic?

I've found that there are generally two approaches: use `void*` or use [lots and lots of macros](https://github.com/NetBSD/src/blob/trunk/sys/sys/queue.h). The void pointer approach leads to more readable code (imo) but has poorer performance due to the pointer indirection even when the type is something like an `int32_t`. The macro style looks really annoying to write but is more performant. 

An example of the `void*` approach is the popular [uthash](https://troydhanson.github.io/uthash/) library. A great example of the macro approach is attractivechaos' [klib](http://attractivechaos.github.io/klib/#Khash%3A%20generic%20hash%20table) (who I highly recommend you check out!).

Which would I choose? I'm not sure yet but I am leaning towards the macro approach for the performance. Personally, a "good enough" but fully implemented hashtable is better than no hashtable.

# Better default RNG

C's default random number generator (RNG) is really quite lacking. Currently the interface is the following:

```C
#include <stdlib.h>

void srand(unsigned seed);  // seed rand
int rand(void);             // generate pseudo random number, (NOT THREAD-SAFE)
int rand_r(unsigned *seed); // thread-safe rand
```

A more convenient interface would look like the following:

```C
bool pry_random_bool(struct pry_rng *rng);
int32_t pry_random_int32(struct pry_rng *rng);
int32_t pry_random_int32_between(struct pry_rng *rng, int32_t low, int32_t high);
```

My plan is to implement PCG, a family of simple but "statistically good" RNGs! Additionally, as perhaps an overoptimization, I would also like to implement [Daniel Lemire's method of mapping uniformly random bits to a specific interval](https://arxiv.org/abs/1805.10941).

The [PCG website](https://www.pcg-random.org/download.html) even has a very minimal implementation which shows how easy PCG is to implement:
```C
// *Really* minimal PCG32 code / (c) 2014 M.E. O'Neill / pcg-random.org
// Licensed under Apache License 2.0 (NO WARRANTY, etc. see website)

typedef struct { uint64_t state;  uint64_t inc; } pcg32_random_t;

uint32_t pcg32_random_r(pcg32_random_t* rng)
{
    uint64_t oldstate = rng->state;
    // Advance internal state
    rng->state = oldstate * 6364136223846793005ULL + (rng->inc|1);
    // Calculate output function (XSH RR), uses old state for max ILP
    uint32_t xorshifted = ((oldstate >> 18u) ^ oldstate) >> 27u;
    uint32_t rot = oldstate >> 59u;
    return (xorshifted >> rot) | (xorshifted << ((-rot) & 31));
}
```

# String and String functions (that won't kill you)

Enough has been said about the [many](https://lwn.net/Articles/225108/) [problems](https://randomascii.wordpress.com/2013/04/03/stop-using-strncpy-already/) with C's string functions. There's even a [paper published](https://www.openbsd.org/papers/strlcpy-paper.pdf) about OpenBSD's `strlcpy` which is designed to be a safe alternative to `strcpy` and `strncpy`. Every C programmer seems to have their own managed C string library like Antirez's [SDS](https://github.com/antirez/sds) or [utf8.h](https://github.com/sheredom/utf8.h) (a collection of UTF-8 string library functions).

One important thing to consider when building such a string library is: how compatible do you want to be with standard C null-terminated strings? SDS, for example, stores metadata in front of a normal `char*` buffer but always returns a pointer pointing to the buffer. This makes the library compatible with functions which deal with null-terminated strings.

I may end up making my own string library at some point since it is just a rite of passage for C programmers. It builds character ya know? I will be taking lots of inspiration from SDS and OpenBSD's string functions. A key goal will be to check for the sorts of buffer overflow problems that C's string functions will quietly allow.

# Arena allocator and passing in allocators
Every so often, I will become obsessed with different types of memory allocators. It's a very rich field and the problems are quiet fundamental. Every runtime needs to deal with the problems of memory allocation and/or garbage collection. While learning about [Zig](https://ziglang.org/), I came across the idea of an arena allocator. If you haven't heard of them before, I highly recommend Ryan Fleury's [great blog post](https://www.rfleury.com/p/untangling-lifetimes-the-arena-allocator) or [talk](https://www.rfleury.com/p/enter-the-arena-talk) on the subject.

By using an arena allocator to batch lifetimes and allocations, you can end up writing C with less worrying about memory management. You will still need to be mindful of lifetimes but by using different arenas, the problem becomes much clearer.

Additionally, a *great* feature of Zig is that all of the stdlib functions which might heap allocate take in an allocator argument. For example:

```Zig
const std = @import("std");
const ally = std.testing.allocator;

// here we are passing in an allocator for the ArrayList!
var list = std.ArrayList(u32).init(ally);
defer list.deinit();
```
(taken from the front page example of https://ziglang.org)

When designing any library for C, I would strongly urge you to also allow the user to pass in an allocator structure. It can be something as simple as:
```C
struct my_allocator {
    void *(*my_malloc)(size_t);
    void *(*my_realloc)(void*, size_t);
    void (*my_free)(void*);
};

int my_heap_allocating_function(/* whatever args here*/, struct my_allocator *alloc);
```
with a struct of function pointers to a malloc, realloc, and free function.

I think that I would personally implement an arena allocator on top of the standard system-provided malloc and in addition, allow users to pass in their own malloc implementation instead (maybe [jemalloc](https://jemalloc.net/) or [tcmalloc](https://google.github.io/tcmalloc/overview.html))!

# Consistent errors
One of my biggest annoyances with writing any C at all is the error handling. It really is hard to go back after being exposed to Algebraic types like those in [Rust](https://doc.rust-lang.org/std/result/) or [OCaml](https://ocaml.org/manual/5.2/api/Result.html). 

There are several possible ways of handling errors in C and really not a single one is that satisfying. You can:
* Return an error value like an `int` or a `ssize_t`. (This requires that your function has some sort of sentinel value like 0 or -1 which cannot be a valid return value if your function is returning a value. This is why `ssize_t` even exists.)
* Pass in an error struct or variable by reference and set it within the function. For whatever reason, this feels clunky to me? But it still beats having to look up what value returned is an error or not. Is it 0? -1? NULL? Who knows!?!?
* Something like errno. But preferably stored within some sort of library context struct. For example, if I have a hashtable, I can add an errno directly to the hashtable structure with no downside that I can see. If you are instead storing something in a global error variable, you will need to be careful in the case of multiple threads writing to the same variable. Always consider whether what you are writing is thread-safe or not and if not at least mention that your function is not thread-safe! 


# Testing harness
Finally, while making such a personal standard library, you'll need to test it. Right? You will test your code right??? So to do this, it makes sense to have a small, minimal unit testing harness. Something like [tau](https://github.com/jasmcaus/tau) or [utest.h](https://github.com/sheredom/utest.h) which are my favorite minimal testing libraries. 


# Conclusion
This was really an excuse to organize my thoughts on useful libraries that I always reach for. I'm really curious to hear your thoughts...
 * Anything I missed that you need often?
 * Anything you disagree with?
 * or even just more cool libraries that I haven't mentioned

Also finally, I plan to most more of these "stream of consciousness" blog posts just to get back into the habit of writing more consistently. Please give me feedback and let me know what works and what doesn't. Thanks.

-- pry