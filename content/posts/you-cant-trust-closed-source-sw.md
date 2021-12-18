---
title: "You Can't Trust Closed Source Software"
date: 2021-12-18T16:06:56-06:00
---

Open source software, [Free software](https://www.gnu.org/philosophy/free-sw.html), whatever you want to call it, is often better than its proprietary counterparts when security, privacy, and performance are being considered. The primary downsides of using open source software is that it is often less immediately accessible and the UIs lack polish. (This is why we need [more designers, artists, and other non-programmers](https://allcontributors.org/) in the open source world!)

But one benefit of open source software that I've seen overlooked by many is that open source software is more *verifiably private*. What do I mean by verifiably private? With open source software, one can download the entire source code and look and see if the program is doing anything sketchy (like [phoning home](https://en.wikipedia.org/wiki/Phoning_home) for instance).

What is even better is that due to the nature of open source software and the ethos around it, if you are using a sufficiently popular project, other people have already looked through the code to see if anything sketchy was happening and would let people in the community know.

This is not the case with proprietary software. Suuuure, privacy audits exist but this just pushes the burden of trust off to whether or not you trust the particular firm that is doing the security audits.


Also, yes, it is possible to  use something like Wireshark and detect whether a program is connecting to an external server,  but this is far less guarantee that a program will always respect your privacy. There is no way without having access to the actual source code of a program to show that it never transmits your data to MindControlAdTech Inc. Companies like Apple can try to align themselves as “privacy oriented” and say [“privacy is a core principle of ours”](https://www.apple.com/privacy/) and so on but without releasing the source code for their programs, their promises are empty.

-- pry
