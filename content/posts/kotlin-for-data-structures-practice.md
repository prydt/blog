---
title: "Kotlin for Data Structures Practice"
date: 2021-11-16T16:19:48-06:00
---

# or Why Kotlin?

The main difference between a computer scientist and a programmer is similar to the difference between an artist and a painter. A programmer merely writes programs, while a [computer scientist creates art](http://www.paulgraham.com/knuth.html). This art is a subjective matter but I do find that each programmer has their own [taste](https://github.com/mkirchner/linked-list-good-taste) and one particular axis by which I measure the beauty of code is whether it uses the right abstractions and whether it is efficient without being sloppy.


To become a better artist, I need to become better at data structures and algorithms. The best way to improve is to practice and actually write code. While a data structure may be conceptually simple, the devils in the details and something like a Binary Search Tree, while seemingly simple, can come with very many complications when actually implementing it (see [BST deletion algorithms](http://www.mathcs.emory.edu/~cheung/Courses/171/Syllabus/9-BinTree/BST-delete.html)).

For this sort of practice, I wish to be pragmatic and use the right tool for the job. I also want to be using a tool that is a joy to use. Thus, as much as I like C or Python, I have decided to practice writing data structures in Kotlin.


## Why not X?


C lacks generics so to create a generic container library, I would have to resort to either using `void*` or macros. While `void*` are simpler, they also come with an performance cost and are thus not preferable, but [writing generic data structures with macros](https://attractivechaos.wordpress.com/2008/09/02/generic-programming-in-c/) (i.e. literally writing your entire data structure within [one big macro](https://svnweb.freebsd.org/base/head/sys/sys/tree.h?view=markup) and having the type be a parameter into that macro) sounds like literal hell.

Python is a delightful language but for my practice, I would prefer static typing. You can write Python with something that approximates static typing but it feels like something bolted-on as an afterthought. (See: [function annotations](https://www.python.org/dev/peps/pep-3107/) and tools like [pydantic](https://github.com/samuelcolvin/pydantic/))

C++ is a language I actually have used in the [past](https://github.com/prydt/algorithms-cpp) for this sort of endeavor but that middle school project fell out of favor for me when I opened the repo up for hacktoberfest pull requests. C++ is the closest thing to C with generics that I would actually like but the templating system is still poorly designed. Understanding how [std::less](https://en.cppreference.com/w/cpp/utility/functional/less) or any of those sorts of things work in the standard library is a pain and there seems to be no real good standard way of specifying a type to be comparable or etc. There’s the way that the C++ standard library does it, but I don’t want to write code that looks like that.

> Within C++, there is a much smaller and cleaner language struggling to get out.” – Bjarne Stroustrup

And I would claim that that language is Java.

Java is a language that I *reluctantly* like. I like parts of it and I think that the OOP model that it goes with is actually quite well designed and not a clusterfuck like C++’s templates. The main things I abhor about Java are:
 * It’s XML infested ecosystem (I might even prefer npm to whatever the fuck Maven is doing… ok no that's going too far.)
 * It’s verbosity.
 * The really terrible design patterns that seem to be commonplace in many many Java codebases. If I ever have to work with an [AbstractSingletonProxyFactoryBean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/framework/AbstractSingletonProxyFactoryBean.html), I'm actually going to strangle somebody.

The solution?

Enter [Kotlin](https://kotlinlang.org/).

Kotlin is a relatively newer language than all the ones that I’ve mentioned but I believe that it is the best language for me to practice building data structures in.

Kotlin runs on the JVM, compiles to JS or Java, and even compiles to native binaries via LLVM.

Kotlin
 * is far less verbose than Java (see [data classes](https://kotlinlang.org/docs/data-classes.html))
 * has more functional programming niceties (ie [map, filter, reduce](https://kotlinlang.org/docs/collection-filtering.html))
 * has AMAZING [null safety](https://kotlinlang.org/docs/null-safety.html#checking-for-null-in-conditions) features (ie no more NullPointerExceptions!)
 * has the same Java generic system that I enjoy using
 * is just a [joy to use](http://steve-yegge.blogspot.com/2017/05/why-kotlin-is-better-than-whatever-dumb.html)


I see Kotlin as an amazing language simply because it’s what Java should’ve been. I don’t even personally care for the flawless interop with Java, but it’s just a great language. I plan to enjoy using it when studying data structures and algorithms.

Also PS: I was also considering [Crystal](https://crystal-lang.org/) and honestly I think it is an awesome language, I just know that there are many good options and at the moment Kotlin looks to be the best. I've already spent too much time thinking over this and I will now go head first into my studies rather than focusing on what technology is being used.
