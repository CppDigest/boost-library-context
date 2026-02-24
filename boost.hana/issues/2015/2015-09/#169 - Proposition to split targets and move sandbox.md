# #169 - [Build targets] Proposition to split targets and move sandbox [Closed]

> Username: m-j-w  
> Created at: 2015-09-01 00:35:30 UTC  
> Updated at: 2015-09-16 17:54:02 UTC  
> Closed at: 2015-09-16 17:54:02 UTC  
> Url: https://github.com/boostorg/hana/issues/169  

Just because I stumbled over it while working on #167, I would like to propose to  
- move <hana>/test/sandbox to <hana>/experimental  
- create new target 'experimental' and 'test_experimental' not included in default target 'ALL'  
- create new target 'test_examples' not included in default target 'ALL'  
- remove target 'examples' from default target 'ALL'  
- create a new target 'full_check' including 'ALL', 'test_examples' and 'test_experimental'  
  
Reason: Experimental code shouldn't belong to unit tests, similarly examples may not be in general compilable. The correctness of the code should solely be verified by unit tests and hence the default target.  
  
Regards, Markus.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-02 16:03:44 UTC  
> Url: https://github.com/boostorg/hana/issues/169#issuecomment-137145045  

I do agree that experimental code does not have its place as part of the unit tests. However, I disagree for the examples. It should be possible to copy/paste the examples as-is from the documentation, which can only be checked if they are considered as "tests". My preferred resolution would be to  
1. Promote some experimental stuff to examples. For example, we're missing examples of creating custom containers. I could move the `Tree` implementation into `example/`, and that would resolve #143.   
2. Remove other things completely. For example, `sandbox/string.cpp` should probably be removed, since it's essentially `std::string_view`. I think most of the stuff in sandbox/ is safe to remove at this point, but I'll need to have a look before I can be sure. Note that this would also remove much of the need to workaround the lack of exceptions in #168.

---

## Comment 2

> Username: m-j-w  
> Created at: 2015-09-02 19:41:12 UTC  
> Url: https://github.com/boostorg/hana/issues/169#issuecomment-137223454  

You're right, with all points. Although a dedicated Sandbox might be handy for other modules/datatypes until they are stable enough to go into the actual includes. More difficult examples not belonging into the manual and demanding for other dependencies could also go somewhere else on the web.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-09-16 17:54:02 UTC  
> Url: https://github.com/boostorg/hana/issues/169#issuecomment-140820567  

I created a dedicated sandbox in `/experimental`, so it does not look like these are unit tests. However,  I still left those targets associated to the `check` target, because I really want to be notified if any of this experimental stuff fails for some reason. While fixing other issues, I hope to get rid of as much stuff as possible in the experimental/ directory.
