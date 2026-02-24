# #200 - [Concepts] Make Printable part of the library instead of an example or tutorial [Closed]

> Username: m-j-w  
> Created at: 2015-11-09 18:44:21 UTC  
> Updated at: 2015-12-10 01:03:26 UTC  
> Closed at: 2015-12-10 01:03:26 UTC  
> Url: https://github.com/boostorg/hana/issues/200  

In the examples and tutorial there is that nice `Printable` concept, which I'd love to see as being part of the main library. Presumably most developers would require something like that, in particular since template metaprogramming provides a fairly small toolset for debugging.  
While it might be difficult to determine a general syntax for the more specialized concepts like mathematical operations, the basic containers would be a good start. Everything else could default to a demangling of the type, just like in the example `tutorial/misc/printable.cpp`.  
Also, `boost::core::demangle` might be a better solution, if available; `abi::__cxa_demangle` is sometimes a little bit inaccurate on cv-qualifiers and, I think, on references.  
A further addition to the `print(...)` function might be a `typewriter(...)` which just converts the demangled type to a string ready for printing, again for the same reason of providing a simple tool to debug or ensure correctness of the compile-time computed types.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-13 18:00:41 UTC  
> Url: https://github.com/boostorg/hana/issues/200#issuecomment-156502286  

This would be useful, definitely.  
  
The main issue I have with providing this functionality is that I want Hana to be usable on embedded systems, even bare-metal ones. If this was provided, it couldn't be part of the main interface of the library, but it would have to be provided as an "extension" or something instead. That poses a problem for the organization of the library. Of course, I could just throw in an `extension/` subdirectory or use the existing `ext/`, but I want to properly update my mental model of the library's organization if I am to do that.

---

## Comment 2

> Username: m-j-w  
> Created at: 2015-11-13 18:34:36 UTC  
> Url: https://github.com/boostorg/hana/issues/200#issuecomment-156511975  

Totally acceptable for a library establishing such a different programming paradigm compared to the conventional run-time style. Not everything has to be in the very core.  
Consider the following for these special purpose concepts: Add a directory `include/boost/hana/utilities/` and place there a single header file that adds all of the additional functionality, say a `printer.hpp`. That could then contain the concept definition, as well the for example `#ifdef BOOST_HANA_TUPLE_HPP` ... `#endif` enclosed overloads of the `print_impl` (tuple being a placeholder here). It would not be included in the main `include/boost/hana.hpp` or any other hana header. This way, a user would still only have to add that single include to the source.  
  
Furthermore, such compressed special purpose concepts would also serve as a perfect tutorial, since not beeing scattered over so many different files. Lastly, and for the very case of the printable concept, a user could simply copy that file, search and replace `print` > `python`, change a few things on the output style, et voila, there's the python printable. (think of python as a placeholder for any user-desirable format).  
  
I'm pretty sure there are many other possible areas. Some of which would turn out to be significant enough in their usefulness and having a widespread enough community of users to actually add them as, say, such a core add-on utility.  
Basically, if something is somewhat expected to be written in code by nearly every programmer in the process of nearly every major project, it might be a candidate, although right now only printable comes to mind...  
(I wouldn't choose `/ext` or `/extension` since that might be misinterpreted with `include/boost/hana/ext/`)
