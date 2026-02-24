# #257 - English the copy-on-write documentation a little more [Closed]

> Username: drok  
> Created at: 2025-04-07 18:04:57 UTC  
> Updated at: 2025-04-18 00:18:14 UTC  
> Closed at: 2025-04-14 20:05:38 UTC  
> Url: https://github.com/boostorg/interprocess/issues/257  

The documentation on [Opening managed shared memory and mapped files with Copy On Write or Read Only modes](https://www.boost.org/doc/libs/1_84_0/doc/html/interprocess/managed_memory_segments.html#interprocess.managed_memory_segments.managed_memory_segment_advanced_features.copy_on_write_read_only) is quite unclear and ambiguous due in large part to linguistic defects.  
  
I'd offer better wording as a PR, but I don't have sufficient expertise in the shared-memory domain to understand how to resolve the ambiguity.  
  
The offending paragraph deals with memory (RAM?) savings arising from the use of copy-on-write semantics with managed shared memory.  
  
https://github.com/boostorg/interprocess/blob/061bc6f8dfc2af90d9996a77317b21398f6bdda5/doc/interprocess.qbk#L4170  
  
I have been avoiding using Boost for a long time due to size and complexity, but I'm coming to love the expertise and insight that went into creating this library. This documentation is my first hands-on contact with your fine work, but the section mentioned above falls a little short of the high standard of quality you've set throughout the body of work.  
  
Thank you, and kind regards!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-04-14 20:06:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/257#issuecomment-2802863609  

Since the report just requires "a little more", I expect the change is in the right direction. A PR is welcome ;-)

---

## Comment 2

> Username: drok  
> Created at: 2025-04-15 04:33:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/257#issuecomment-2803762738  

Thank you. Maybe I should clarify what I find confusing:  
  
> This can lead to substantial memory savings, especially when dealing with large datasets or  
    when many processes need to access the same data with only a few modifying it  
  
This sentence suggests that if two or more processes need to modify the same file, opening it in COW mode instead of regular RW mode would save RAM, since the same memory pages are mapped into all processes' address spaces.  
  
In other words, if you need to write to the same file from more than one process, the COW mode saves memory (without other trade-offs).  
  
But if the written sections are private to each process, rather than shared, doesn't this use more memory? Also, would this not create the tradeoff that the modified pages need to be somehow merged when saving to the file, making the inter-process communication more complicated? Additionally, COW mode would prevent each process from seeing the changes made by the other processes, meaning there is no "inter-process" communication as such? Doesn't COW mode prevent inter-process communication via shared memory regions?  
  
So if I wanted to have several processes write the same file, and I wanted to save memory, I wouldn't use COW simply as a RAM-savings technique, because I would not only need to implement merging, but also the private modified pages would consume more RAM than if I opened the file in RW mode?  
  
Thanks

---

## Comment 3

> Username: igaztanaga  
> Created at: 2025-04-17 19:33:09 UTC  
> Url: https://github.com/boostorg/interprocess/issues/257#issuecomment-2813848352  

COW mode does not share modifications: "shared memory or file is opened as read-only" The modification made by each process is kept in private memory, the underlying file/shared memory is not modified.  
  
Memory is saved because instead of having a private copy of the whole file/shared memory in each process, unmodified parts are shared from a single shared memory. In the very moment a process modifies a page, then a private copy of that page must be  performed to keep modifications private (increasing memory usage). All other processes not modifying that page share the original, read-only page.

---

## Comment 4

> Username: drok  
> Created at: 2025-04-18 00:18:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/257#issuecomment-2814239379  

Memory is saved because instead of accessing a **single** shared copy, each process has its own, separate, private copy of the same data ?!  
  
So two processes `mmap`-ing the same file for `PROT_WRITE`|`PROT_READ` require more physical RAM (in total) than two processes `mmap`-ping the same file in COW mode, each with private resident memory pages? That makes no sense to me, and your answer does not clarify this exact basic circumstance.  
  
Holy magic cow.
