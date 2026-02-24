# #288 - Hitting the wall; what am I doing wrong? [Open]

> Username: LegalizeAdulthood  
> Created at: 2025-11-03 04:41:05 UTC  
> Updated at: 2025-11-05 15:41:35 UTC  
> Url: https://github.com/boostorg/parser/issues/288  

## Hitting the Wall when Compiling the Grammar  
  
I've been developing a parser for a fractal renderer formula file.  Things were progressing nicely as I started parsing larger and larger elements of the format until I seemingly hit a wall.  As of commit [2ef0a54](https://github.com/LegalizeAdulthood/formula-compiler/tree/2ef0a54ccc06d46681bf9430d8ede785d0994db1) on the [formula-compiler](https://github.com/LegalizeAdulthood/formula-compiler) repository, I can't seem to add any more rules or the compilation just hangs on msvc.  Note that it doesn't error, it just seems to get stuck compiling.  I don't see any high activity bars on my disk or my CPU, so I'm not sure what the compiler is doing.  But it ran for many minutes and didn't finish compiling parser.cpp.  
  
## Separate Compilation?  
  
The header parser_fwd.hpp made me think that I might be able to break my parser into multiple source files and reduce the pressure on template mechanisms.  I had noticed that the compile time for the parser was increasing as I added more details into my parser, but I considered this normal, especially as I was building Debug.  However, I tried my best to separate out a simple rule into a separate translation unit and was unsuccessful.  GitHub Copilot gave it a try, but only flailed around for 30 minutes before I put it out of it's misery.  
  
Is there a mechanism for separate compilation of parts of a grammar?  None of the examples demonstrate separate compilation.  
  
## My AST is a Hierarchy of Node shared_ptrs  
  
The documentation recommends using the synthesized attributes for rules, but there doesn't seem to be automatic attribute propagation to shared_ptrs as the attributes.  Therefore I need semantic action builder functions to create the attribute in my AST from the attribute synthesized by the framework.  Is this putting additional template pressure on the compiler?  
  
## How Does the Number of Rules Impact Compilation?  
  
I can create local parsers, e.g. `const auto alpha = char_('a', 'z') | char_('A', 'Z');` without rules and use them to build productions that generate AST nodes.  I also have a bunch of rules (see [parser.cpp line 187](https://github.com/LegalizeAdulthood/formula-compiler/blob/2ef0a54ccc06d46681bf9430d8ede785d0994db1/libs/parser.cpp#L187)).  Should I be preferring fewer rules to reduce template pressure on the compiler or are they the same cost as the local parsers?

---

## Comment 1

> Username: sehe  
> Created at: 2025-11-03 23:03:28 UTC  
> Url: https://github.com/boostorg/parser/issues/288#issuecomment-3482956164  

### My AST is a Hierarchy of Node shared_ptrs  
  
I'd be tempted to assume that, like Spirit, the attributes want to have value-semantics (see e.g. https://stackoverflow.com/a/23301519/85371, https://stackoverflow.com/a/13060402/85371). Especially since exceptions might be raised during parsing, and semantic actions might run several times for branches later rejected etc.  
  
> but there doesn't seem to be automatic attribute propagation to shared_ptrs as the attributes.  
  
This reinforces my intuition.  
  
> Therefore I need semantic action builder functions to create the attribute in my AST from the attribute synthesized by the framework.   
  
You could borrow from https://stackoverflow.com/a/21565350/85371 (also employed here: https://stackoverflow.com/a/61205788/85371)  
  
> Is this putting additional template pressure on the compiler?  
  
All code is, so yes.  
  
### Separate Compilation?  
  
Interestingly, X3 **does** support that, by way of [BOOST_SPIRIT_DECLARE](https://www.boost.org/doc/libs/latest/libs/spirit/doc/x3/html/spirit_x3/tutorials/minimal.html#spirit_x3.tutorials.minimal.boost_spirit_declare). Even though the ergonomics of it are... fiendish (see https://stackoverflow.com/search?tab=newest&q=user%3a85371%20BOOST_SPIRIT_DECLARE&searchOn=3).   
  
I cannot find anything in code or documentation to suggest that Boost.Parser has support for this. Likewise, a type-erased rule wrapper like `x3::any_rule<>` doesn't appear to be provided.  
  
### How Does the Number of Rules Impact Compilation?  
  
Reading the docs, it should scale linearly. For x3, frequently (especially with recursive rules and/or nesting contexts) the number would scale > linearly (partly coupled to the number of rule _references_ inside the grammar). However, Boost.Parser seems to promise that different contexts do NOT lead to different behavior, so I would expect them to cause idempotent rule instantiations.  
  
Someone more experienced with Boost.Parser can potentially correct me on these.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2025-11-04 00:06:25 UTC  
> Url: https://github.com/boostorg/parser/issues/288#issuecomment-3483087476  

Thanks for the reply  
  
> I'd be tempted to assume that, like Spirit, the attributes want to have value-semantics  
  
...but isn't that the whole point of using shared_ptrs to give value semantics to heap-allocated data?  IOW, you can copy around the shared pointer as much as you like; as long as you don't snatch out the raw pointer, you should be fine and not leak memory.

---

## Comment 3

> Username: sehe  
> Created at: 2025-11-04 03:09:18 UTC  
> Updated at: 2025-11-04 03:15:13 UTC  
> Url: https://github.com/boostorg/parser/issues/288#issuecomment-3483584315  

I'm assuming you didn't follow the links. I'm also wondering whether you read the rest of the reply. Also, you don't have to _agree_; you already know exactly how to force your preference.  
  
Anyways, in the meantime, I ported the parser to X3 just for kicks. Running the unit tests is about twice as fast for me, and compilation is surprisingly MUCH faster.  
  
Boost.Parser:  
  
<img width="1030" height="281" alt="Image" src="https://github.com/user-attachments/assets/23a70515-3416-4064-934f-4f0b7d7e8cd3" />  
  
Spirit.X3:  
  
<img width="1030" height="281" alt="Image" src="https://github.com/user-attachments/assets/dc4ab801-d746-4940-bb6b-d6fdbb764fa0" />  
  
In case you want to play around yourself, see https://github.com/sehe/formula-compiler/tree/sehe_x3

---

## Comment 4

> Username: LegalizeAdulthood  
> Created at: 2025-11-04 04:37:59 UTC  
> Url: https://github.com/boostorg/parser/issues/288#issuecomment-3483743130  

> I'm assuming you didn't follow the links. I'm also wondering whether you read the rest of the reply. Also, you don't have to agree; you already know exactly how to force your preference.  
  
I did, but instead of being indirect about it, why don't you state directly what you're trying to tell me?  
  
You didn't answer my question about the point of using shared ptrs.

---

## Comment 5

> Username: sehe  
> Created at: 2025-11-04 23:24:00 UTC  
> Url: https://github.com/boostorg/parser/issues/288#issuecomment-3488357807  

@LegalizeAdulthood I am using my time effectively. I took an interest (because you drew attention to this on the boost ML), but I am under no obligation to provide support here. I wish you the best of luck.

---

## Comment 6

> Username: LegalizeAdulthood  
> Created at: 2025-11-05 15:41:35 UTC  
> Url: https://github.com/boostorg/parser/issues/288#issuecomment-3491966574  

That's fine, just that I looked at the links you provided and it didn't directly answer any of my questions w.r.t boost.parser, especially as they are posts about Spirit, which is a different library.
