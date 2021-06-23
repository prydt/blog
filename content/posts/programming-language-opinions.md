---
title: "Programming Language Opinions"
date: 2021-06-22T21:10:18-05:00
tags: ["technology", "fun"]
---

Programming languages to hackers are tools and many craftspeople take great pride in their tools of choice. Tools often come with trade offs and the most skilled craftspeople know precisely when to use a tool and perhaps most importantly, when not to.

>  Each language has its purpose, however humble. Each language expresses the Yin and Yang of software. Each language has its place within the Tao.
>
> But do not program in COBOL if you can avoid it.
>
> -- [The Tao of Programming](https://www.mit.edu/~xela/tao.html)

That being said... here are some opinions on languages that I have used so far.

## JavaScript

JavaScript was my first programming language (through the amazing and beautiful and wonderful Khan Academy). JavaScript is so incredibly useful now that it is *the* website programming language and I genuinely think that parts of the language are cool! The fact that JavaScript has first-class functions is great. Other than that, JS is pretty [messy](https://youtu.be/3se2-thqf-A?t=80) and the ecosystem feels very low quality compared to other languages. NPM is a big source many of my problems with the JS ecosystem and the whole [left-pad situation](https://arstechnica.com/information-technology/2016/03/rage-quit-coder-unpublished-17-lines-of-javascript-and-broke-the-internet/) really shows the overuse of libraries for simple things.

Also, I have very mixed feelings about Node.js because it very much feels like overengineering. I looked at many lists of "why should I use Node.js for my server" and it seems like outside of being good for managing concurrent connections, its just a language that many are familiar with and they shoehorn it into being used on the server side.

I use JavaScript for client-side code only and I use it only when necessary. I don't think JavaScript is inherently evil or even a bad programming language, I just believe that it is overused and this overuse has made many websites more bloated and slow. An example would be using a JS frontend framework like React.js or Vue.js for building a static site. Why???? Just use HTML + CSS and maaaaybe a little JS as a treat.

## Java

Java is an interesting beast. Java feels like a more well-thought-out C++ but is too verbose and runs on a VM.

I learned Java in middle school and even at that age I could tell that object oriented programming wasn't all it was hyped up to be.

> Object-oriented programming is an exceptionally bad idea which could only have originated in California.
>
> – Edsger Dijkstra

`System.out.println` is a perfect [encapsulation](https://en.wikipedia.org/wiki/Encapsulation_\(computer_programming\)) of how terribly verbose Java is. And speaking of encapsulation... It always felt like encapsulation makes sense in some cases but not in every case and it just leads to adding a getter and setter for every private variable.

Learn Java if you want a soul-crushing code monkey job 🤩.

## Kotlin

Kotlin is better than Java in every way and the best thing about it is that it reduces the verbosity of code (features like data classes are awesome!). Even then, I don't really see a use for Kotlin outside of making Android apps.

And honestly, even then making mobile apps sounds like such a pain without something like Flutter or React Native if you want to reach iOS users.

ok ok back to programming languages...

## C

C is one of my favorite languages. I love it because it is a simple language. That being said, it certainly isn't simple to program even the simplest of things in it. Working with strings in C is a nightmare. And while C does have incredible libraries for everything you could possible want, C doesn't have a singular package manager and instead depends on individual linux distro package managers. This makes it a pain to run if a library isn't packaged for your distro.

Also, this is a minor point, but I would really love it if C had generics. I know that it would make the language more complex but generics are the one feature that I really miss when writing a data structure in C. Yes, I could write the whole data structure using `void*` but that incurs a [performance cost](https://attractivechaos.wordpress.com/2008/10/02/using-void-in-generic-c-programming-may-be-inefficient/). I could also write the entire data structure [in a macro](https://github.com/attractivechaos/klib#methodology) and then use that to emulate gnerics but this feels like a hack without many of the niceties of generics.

C is great if you are careful, patient, and are ok with writing many things yourself and only reaching to libraries as a last resort.

## C++

I hate C++. There are parts that I like but overall the language just feels *too* complicated.

> Whenever the C++ language designers had two competing ideas as to how they should solve some problem, they said, “OK, we’ll do them both”. So the language is too baroque for my taste.
>
> – Donald E Knuth

It does too many things half-well and other things poorly. It is very useful in AAA game development and in many applications but I strongly dislike how much of a lovecraftian amalgamation it is.

C++ is a clear example of feature creep.

## Python

Python is a wonderful language for getting things done. It is easily the programming language I am most productive in just because of how powerful the core data structures built-in to the language along with the "batteries-included" standard library. The `dict` and `list` types are so powerful! Working with XML or JSON or sqlite or gzip or LZMA or CSVs or ...? Well they are all supported in the standard library. And I haven't even gotten to the incredible ecosystem of lirbaries made for python. [NumPy](https://numpy.org/), [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/), and [Flask](https://flask.palletsprojects.com/en/2.0.x/) make Python a joy to work with.

Some people complain about the issue of the [Python Environment](https://xkcd.com/1987/) and I'm sure it was true in the past, but with the advent of [venv](https://docs.python.org/3/library/venv.html), I never have any issues with setting up Python.

Python is my Swiss Army Knife when it comes to anything that I want to do quickly and easily. The downsides of Python to me is that it is quite large and wouldn't be a good fit for embedding into other programs as a scripting language and in general it is quite slow compared to complied languages.

## Lua

Lua feels like Python done right(tm). While the standard library and ecosystem aren't as vibrant or large, the language itself feels very well-designed and small. Lua is just an adorable language, easily embeddable which makes it the go to choice of many video games as a scripting language.

The singular sin of Lua to me is the fact that it is 1-indexed. WHAT MONSTER WOULD DO SUCH A THING? To take such a beautiful language and introduce a singular syntactic slipup.

Also, I just have to mention [Love2d](https://love2d.org/) which is an amazing little framework to get starting making video games! I highly recommend it to everyone.

All in all, Lua is an incredibly powerful and small scripting language which definitely has its uses.

## PHP

PHP is a much hated language. And with good reason. It is a terrible language with a terrible standard library and terrible performance. It was so unscalable for Facebook, Facebook wrote their [own transpiler](https://en.wikipedia.org/wiki/HipHop_for_PHP) and eventually a [JIT VM](https://github.com/facebook/hhvm/) for it.

And [most of the known internet runs on PHP](https://w3techs.com/technologies/details/pl-php). PHP is a reminder that the best technology does not always win out in the end and convenience plays a big factor in how popular something gets.

Don't use PHP if you can avoid it.

## C#

C# seems like Microsoft Java. To be fair to C#, it feels like a much better designed language than Java. One of my main criticisms would be that it is too tied to a singluar company but since Microsoft open-sourced .NET, this is less of an issue.

## Crystal

Crystal is a language that I really want to succeed. The promise of "Fast as C, Slick as Ruby" is extremely compelling (even if I don't find Ruby that slick). Crystal is actually extremely fun to use. The language is exciting and fast and genuinely feels like an amazing combination of C and Python.

I have always wanted a language like Python or Lua but as a compiled language and Crystal is an ongoing project to create something like that. 

The biggest issues with Crystal are that it has only [reached a 1.0 release recently](https://crystal-lang.org/2021/03/22/crystal-1.0-what-to-expect.html). Both the language and the ecosystem are young and while I hope it will grow into something amazing, I am not holding my breath because to my knowledge, there still isn't a major backer to the project. All the languages I have mentioned above are used by thousands of extremely large companies and many new languages are backed by a large organization like Rust with Mozilla or Go with Google and Crystal doesn't seem to be all that used yet.

## The End

I am pragmatic about my use of languages and honestly, most criticism of a language that I have made here are due to expecting the wrong things from a language. You shouldn't expect a dynamically typed scripting language to be good at extremely fast computations and you shouldn't expect C to be ergonomic to use with JSON out of the box.

I know this was quite rambly but I hope it gave even a little joy or insight into my views on these varying technologies. I hope to update this or write a sequel later once I learn many more languages like:
 * Rust
 * Elixir
 * Scheme (and other lisps?)
 * Haskell
 * Verilog
 * Go
 * etc!

-- pry
