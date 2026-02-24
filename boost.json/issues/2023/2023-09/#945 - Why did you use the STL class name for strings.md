# #945 - Why did you use the STL class name for strings? [Closed]

> Username: dannypike  
> Created at: 2023-09-29 07:15:58 UTC  
> Updated at: 2023-09-30 04:55:56 UTC  
> Closed at: 2023-09-29 07:49:03 UTC  
> Url: https://github.com/boostorg/json/issues/945  

### Version of Boost  
  
#define BOOST_VERSION 108300  
  
### Steps necessary to reproduce the problem  
  
Your documentation suggests that I use:  
```  
using namespace std;  
using namespace boost::json;  
```  
  
However, your library defines a class that has the same name class ("string") as the STL library. So I get ambiguity errors from the compiler if I "use" both namespaces.  
  
### All relevant compiler information  
  
I'm really surprised that you haven't noticed this, so maybe I forgotten something about how this works (it's quite a few years since I last used Boost)?  
  
However, if this is an oversight on your part, would it be possible for you to publish a version of your library that does not clash with the standard library class names?   
  
Thanks :)

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-09-29 07:45:47 UTC  
> Url: https://github.com/boostorg/json/issues/945#issuecomment-1740457238  

Can you point to a the page where we suggest `using namespace std;` ? I can't find it anywhere in the sources.  
  
`using namespace boost::json;` is something we do _for the documentation_. Otherwise all snippets would have had very long identifiers that are harder to read. We also omit necessary includes for snippets to work. It's done to just remove clutter. We do not actually suggest you do that in your code.  
  
I personally only use `using namespace` for namespaces that are meant for that (namespaces with special operator overloads like `std::chrono_literals` or namespaces with special variables like `std::placeholders`). And for other namespaces I just use aliases e.g. `namespace json = boost::json;`.  
  
Now, to your actual question. Vinnie called it `string`, because it's for JSON strings, simple as that.

---

## Comment 2

> Username: dannypike  
> Created at: 2023-09-29 07:49:03 UTC  
> Url: https://github.com/boostorg/json/issues/945#issuecomment-1740460894  

I saw it in the [Quick Look](https://www.boost.org/doc/libs/1_83_0/libs/json/doc/html/json/quick_look.html). It's at the end of the first paragraph.  
  
I thought I must have misunderstood something.  
  
Reading it again now, I can see your point that it's only to reduce clutter. I took it as a suggestion that this is the way that I should write the code. Thanks for the clarification!

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-09-29 07:51:18 UTC  
> Url: https://github.com/boostorg/json/issues/945#issuecomment-1740463384  

> I saw it in the [Quick Look](https://www.boost.org/doc/libs/1_83_0/libs/json/doc/html/json/quick_look.html). It's at the end of the first paragraph.  
  
I triple checked, but there's no `using namespace std;` specifically there.

---

## Comment 4

> Username: RoyBellingan  
> Created at: 2023-09-29 10:16:40 UTC  
> Url: https://github.com/boostorg/json/issues/945#issuecomment-1740651992  

//sometimes you have multiple json lib at the same time so...  
namespace bj = boost::json;

---

## Comment 5

> Username: dannypike  
> Created at: 2023-09-30 04:55:56 UTC  
> Url: https://github.com/boostorg/json/issues/945#issuecomment-1741678296  

Yes, thank you. That's what I'm doing now. I was just a bit confused by what I read in the "Quick Look", is all.
