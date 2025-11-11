---
title: Programming languages are not just a tool
published: 2025-11-11T20:39:45.304Z
description: 'Many think that programming languages are just a tool, today, I want to bring a different view on this discussion.'
tags:
  - programming
  - discussion
draft: false
toc: true
lang: 'en'
---

While I was browsing LinkedIn this week (I know!), I came across a post that
discussed the idea that people consider programming languages to be just a
tool, which, in my view, is a completely wrong conception.

Unfortunately, I don't have the link to the post to reference here, but I think
the idea of the debate is still valid for discussion. I vaguely remember that
the post mentioned some different programming languages to try to justify its
point, which I believe is the most reasonable argument for the debate, since if
we analyze language by language, we can notice that, even though many have a
common ancestor (the C language), their ideation and conception are totally
different.

Let's take the example of Rust[^1] and Python[^2], both are popular languages
used in various open (and closed) source projects, however, the nature of both
languages differs greatly, even though they can be used for the same purpose,
in which Rust has a focus on performance, memory safety and stands out in
embedded systems, while Python has an extreme appeal for prototyping, data
science and is included in practically every current operating system.

Not that the possibility of reproducing the same idea in different languages is
impossible, but thinking that languages are merely tools that can be swapped
between projects, and that they will continue to work the same way, is not a
constant or absolute truth. Can you create an operating system[^3] in Rust? The
answer is yes! Can you do the same with Python? Technically possible[^4], but
extremely impractical due to the interpreted nature and overhead of the
language.

In some projects, you will need the performance of language X, or the ease of
use of language Y, or even the availability of language Z, making your
project's architecture change considerably depending on your choice, even the
way you think about your programming is modified depending on the use of a
particular language.

Thinking this way, we have to take into consideration the aspect of language
paradigms; when we work with functional languages, we have a more immutable way
of thinking, which differs completely from object orientation, since in it, we
think more about structures, code coupling, etc.

Another very important point is the seniority of your team (considering a
commercial project) with the language in question. If you have a team that is
very efficient and experienced with Python, why risk building a system in Go,
for example? You will end up reducing your team's performance and autonomy,
sacrificing important (human) resources simply for the sake of the idea that
"languages are just tools".

# References

[^1]: https://rust-lang.org/
[^2]: https://www.python.org/
[^3]: https://os.phil-opp.com/
[^4]: https://github.com/crcollins/pyOS