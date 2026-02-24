# #69 - python_for_extensions should use -undefined dynamic_lookup on darwin [Closed]

> Username: tdsmith  
> Created at: 2015-04-17 06:49:10 UTC  
> Updated at: 2015-05-05 07:03:26 UTC  
> Closed at: 2015-05-05 07:03:26 UTC  
> Url: https://github.com/boostorg/build/issues/69  

build/src/tools/python.jam says:  
  
>    # Unlike most *nix systems, Mac OS X's linker does not permit undefined  
>     # symbols when linking a shared library. So, we still need to link against  
>     # the Python framework, even when building extensions.  
  
Passing `-undefined dynamic_lookup` to the linker (instead of -lpython or -framework Python) permits undefined symbols and achieves the desired behavior. This has existed [since OS X 10.1](http://www.cmake.org/pipermail/cmake/2005-March/006249.html) so there should be no compatibility concerns.  
  
Linking a module against one Python framework and importing it from another (ABI-compatible) interpreter causes the interpreter to segfault, so the current behavior is a pain point for e.g. Homebrew.

---

## Comment 1

> Username: asparagui  
> Created at: 2015-04-23 06:58:03 UTC  
> Url: https://github.com/boostorg/build/issues/69#issuecomment-95465460  

What he said.

---

## Comment 2

> Username: vprus  
> Created at: 2015-04-23 07:42:27 UTC  
> Url: https://github.com/boostorg/build/issues/69#issuecomment-95478702  

Anybody can submit a pull request, along with comment how it was tested?  
While the change seem simple, I'd rather not commit things I can't test.  
  
Thanks,  
On Чт, 23 апр. 2015 at 9:58 Brett Koonce notifications@github.com wrote:  
  
> What he said.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/issues/69#issuecomment-95465460.

---

## Comment 3

> Username: vprus  
> Created at: 2015-05-04 14:30:39 UTC  
> Url: https://github.com/boostorg/build/issues/69#issuecomment-98727145  

Tim,  
  
did you have a time to create a patch?

---

## Comment 4

> Username: tdsmith  
> Created at: 2015-05-04 16:03:43 UTC  
> Url: https://github.com/boostorg/build/issues/69#issuecomment-98766461  

I hadn't tried but I think I see how to do it. I don't have a boost development environment set up. I propose to test by making this change in the latest boost release tarball, build Boost.Python, and demonstrate that an application that uses Boost.Python still functions despite the different linkage.

---

## Comment 5

> Username: tdsmith  
> Created at: 2015-05-05 05:24:20 UTC  
> Url: https://github.com/boostorg/build/issues/69#issuecomment-98954910  

Done in #78.

---

## Comment 6

> Username: tdsmith  
> Created at: 2015-05-05 07:03:26 UTC  
> Url: https://github.com/boostorg/build/issues/69#issuecomment-98975851  

Closed by b1c031d26b0e94570511acfdb0dfb8a43f0f6e58 (thanks!).
