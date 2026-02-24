# #276 Migrates the documentation to Asciidoc/MrDocs [Merged]

> Username: anarthal  
> Created at: 2025-06-22 20:04:34 UTC  
> Updated at: 2025-07-03 09:33:01 UTC  
> Merged at: 2025-07-02 21:27:33 UTC  
> Closed at: 2025-07-02 21:27:33 UTC  
> Url: https://github.com/boostorg/redis/pull/276  

close #247

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-06-22 20:05:10 UTC  
> Url: https://github.com/boostorg/redis/pull/276#issuecomment-2994418823  

This is still a very early prototype with lots of things to solve.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-06-26 11:58:05 UTC  
> Url: https://github.com/boostorg/redis/pull/276#issuecomment-3008232627  

@mzimbres I'm trying to fix #247. I'm experimenting with MrDocs. Here's what it'd look like: https://anarthal.github.io/boost-redis-mrdocs/redis/reference.html  
  
The problem is that it looks very experimental yet. I've had to change some correct code to dodge mrdocs segfaults (and this is something that has happened every time I've tried it before). The reference looks beautiful, but it has fewer links than any normal Doxygen reference. If you look closely, you will see some defects. Some of them can be fixed on our side, some of them can't.  
  
What do you think? Do we move forward with this and accept the defects? Or do we set up a PyDocca/quickbook setup as I have in MySQL, which we know it works well?

---

## Comment 3

> Username: mzimbres  
> Created_at: 2025-06-26 17:29:44 UTC  
> Url: https://github.com/boostorg/redis/pull/276#issuecomment-3009228949  

> What do you think? Do we move forward with this and accept the defects? Or do we set up a PyDocca/quickbook setup as I have in MySQL, which we know it works well?  
  
I prefer to move directly to MrDocs and the reference looks great. Is it considered too early to use MrDocs or are they welcoming early adopters? I think we can go forward with this.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-06-30 21:14:17 UTC  
> Url: https://github.com/boostorg/redis/pull/276#issuecomment-3020771677  

Preview: https://anarthal.github.io/boost-redis-mrdocs/redis/index.html

---
