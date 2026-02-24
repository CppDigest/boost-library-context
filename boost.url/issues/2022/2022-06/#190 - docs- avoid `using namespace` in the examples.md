# #190 - docs: avoid `using namespace` in the examples [Closed]

> Username: akrzemi1  
> Created at: 2022-06-06 21:05:17 UTC  
> Updated at: 2022-06-09 20:07:22 UTC  
> Closed at: 2022-06-09 20:07:22 UTC  
> Url: https://github.com/boostorg/url/issues/190  

The docs say that the source code examples should be read as if preceded by declaration:  
  
```c++  
using namespace boost::urls;  
```  
  
This is confusing, because I do not know which name comes from which namespace in examples like:  
  
```c++  
string_view s = "https://path";      // which string_view?  
result<url_view> r = parse_uri( s ); // which result<>?   
```  
  
It would be easier to  grasp, albeit a bit longer, if we had instead:  
  
  
```c++  
namespace urls = boost::urls;  
  
urls::string_view s = "https://path";  
boost::system::result<urls::url_view> r = urls::parse_uri( s );  
```

---

## Comment 1

> Username: sehe  
> Created at: 2022-06-06 21:13:57 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1147937606  

Would it help if the notice also mentions that the doc snippets are actually "live" compiled code in the repository, including a link to that code in context? This is how some other libraries do things, and I think it removes the problem of "mythical context".  
  
I disagree that last code sample is easier to grasp. It's a lot noiser, and a lot harder to see the moving parts. If one insisted I'd go for _in-situ_:  
  
```c++  
using namespace boost::urls;  
  
string_view s = "https://path";  
boost::system::result<url_view> r = parse_uri( s );  
```  
  
But I guess that would get repetitive real quick.  
  
Note also that `string_view` is *any* string-view here (courtesy of Boost Core), so `urls::string_view` is doing the reader a disservice by implying that it needs to be a particular proprietary type.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-06-06 21:18:43 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1147941799  

I'm not sure I understand the problem in  
  
```cpp  
string_view s = "https://path";      // which string_view?  
result<url_view> r = parse_uri( s ); // which result<>?   
```  
  
- which string_view? `urls::string_view`. What else could it be? We're not `using namespace std;`.  
- which result<>? `urls::result`. What else could it be? We're not `using namespace any_namespace_that_contains_some_other_result_type;`.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2022-06-06 21:47:11 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1147965291  

We are discussing the initial page of the docs. At this point the reader is not familiar with the design decisions taken by the authors, or problems like the one with Core.String_view. Everything is new to her. She sees    
  
```c++  
string_view s = "https://path";   
```  
  
And why would it be obvious to her that it is `urls::string_view`? At this point no-one suspects that Boost.URL would define its own `string_view`, given that we have one already in Boost.Utility. Also I hear there is no common understanding between the people who already know the library well. @alandefreitas says it is obvious that we mean `urls::string_view`, whereas @sehe says making it `urls::string_view` is doing the reader a disservice. Also, I do not think the reader will understand that the intention is to say "any string_view".  
  
If the goal of the library is to demonstrate that it works with `std::string_view` and `boost::string_view` alike, then maybe the example should read:  
  
```c++  
boost::string_view s = "https://path";   
```  
  
Similarly with `result<>`. I would not expect that `result<>` is defined in namespace `urls`, given that we have one in `boost::system` and another one in `boost::outcome`. And while it is obvious for you that `result` is from namespace `urls`, the solution proposed by @sehe suggests that it is from `boost::system`. So how a newbie could figure out what namespace it is?

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-06-06 22:03:45 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1147978490  

> And why would it be obvious to her that it is urls::string_view?   
  
Because she's neither `using namespace std;` nor `using namespace boost;`.  
  
> Also I hear there is no common understanding between the people who already know the library well. @alandefreitas says it is obvious that we mean urls::string_view, whereas @sehe says making it urls::string_view is doing the reader a disservice  
  
I don't think these statements are in conflict.   
  
I think @sehe means that **even** if the user interprets that as `std::string_view` that would **still** be OK. Not that users **should** interpret that as `std::string_view`.  
  
> Similarly with result<>. I would not expect that result<> is defined in namespace urls, given that we have one in boost::system and another one in boost::outcome.  
  
`result<>` is even worse because there's no `std::result<>`.   
  
I have the impression you are assuming the user should interpret   
  
```cpp  
using namespace boost::urls;  
```  
  
as  
  
```cpp  
using namespace std;  
using namespace boost;  
using namespace boost::urls;  
using namespace boost::core;  
using namespace boost::outcome;  
```  
  
for some reason.  
  
> So how a newbie could figure out what namespace it is?  
  
Just like a newbie could figure out the examples are not in Python. Because the documentation says it's C++.   
  
It's in `boost::urls` because the documentation says the examples should be read as if preceded by the declaration:  
  
```cpp  
using namespace boost::urls;  
```  
  
There's no reason to assume it's preceded by other declarations just like there's no reason to assume the examples are in Javascript.   
  
In fact, Christian's point about making the "example code easier to copy-paste into "real" projects" seems much more reasonable.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2022-06-06 22:39:24 UTC  
> Updated at: 2022-06-06 22:39:41 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1148004136  

> It's in boost::urls because the documentation says the examples should be read as if preceded by the declaration:  
> `using namespace boost::urls;`  
  
This will be one source of information to a newbie. Another would be their intuition (implicit assumptions), like "this library couldn't possibly add a third type `result` given that we already have `boost::system::result`.  
  
Also, you explanation assumes that the user can trust the library authors that they are very strict an consistent about what they say. But my experience shows that a user starts with a mistrust: Is this library good? Do the examples even compile?  
  
> In fact, Christian's point about making the "example code easier to copy-paste into "real" projects" seems much more reasonable.  
  
In real projects one would likely not want to do a `using namespace`. In real project you usually employ more than one library and you want to know which identifiers came from which library.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-06 22:45:04 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1148006983  

The docs look wrong. `string_view` is used before explaining to the user that code snippets are written as if `using namespace boost::urls;` came before. I suggest the following:  
  
1. Move the `using namespace` callout to the beginning of the page.  
  
2. Add exposition to the callout which reminds the reader that `result`, `error_code`, `string_view`, et. al. are aliases in the `boost::urls` namespace and as such are used without qualification.  
  
3. Add additional exposition explaining that Boost.URL uses `boost::core::detail::string_view` and not `boost::string_view`.  
  
4. Add a new sub-section, after the callout (but not inside the callout) which explains the purpose of the core version of string view, and that the choice of alias is subject to change.  
  
If it doesn't already exist, @pdimov might consider making a page explaining the rationale of `string_view` so that we can link to it instead of repeating it. Also might be nice if Boost.URL links to the page explaining the new std compatibility features of `boost::system::error_code`.

---

## Comment 7

> Username: sehe  
> Created at: 2022-06-06 22:56:31 UTC  
> Updated at: 2022-06-06 22:57:03 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1148012373  

> And while it is obvious for you that result is from namespace urls, the solution proposed by @sehe suggests that it is from boost::system. So how a newbie could figure out what namespace it is?  
  
@akrzemi1 Oof. I did *not* come up with `boost::system::result`. That was you, in the preceding post. I believed you. I guess I should have "starts with a mistrust".  
  
(Side note, I 100% expect `urls::result` to be a type alias for it)  
  
> Also, I do not think the reader will understand that the intention is to say "any string_view"  
  
That's not the intention. The intention is to say "a string_view". And the omission of details means two things:  
  
 - the exact type doesn't matter for the exposition, reader can happily assume whatever they use on a daily basis  
 - the documentation snippet compiles without further qualification

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-06-06 23:56:19 UTC  
> Updated at: 2022-06-06 23:57:35 UTC  
> Url: https://github.com/boostorg/url/issues/190#issuecomment-1148047544  

`boost::urls::result` is an alias, yes:  
https://github.com/CPPAlliance/url/blob/173a32b4476edaf18e96711a601134403060fcd8/include/boost/url/error.hpp#L82  
  
> I do not think the reader will understand that the intention is to say "any string_view"  
  
The intention is not "any string_view" - it is that the string view for which using namespace boost::urls followed by uttering string_view, unqualified, will compile.
