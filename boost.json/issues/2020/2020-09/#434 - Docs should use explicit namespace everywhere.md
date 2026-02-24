# #434 - Docs should use explicit namespace everywhere [Closed]

> Username: uecasm  
> Created at: 2020-09-30 01:37:03 UTC  
> Updated at: 2021-03-04 05:32:42 UTC  
> Closed at: 2021-03-04 05:32:41 UTC  
> Url: https://github.com/boostorg/json/issues/434  

In every single code block in every single page, the types provided by the library should be explicitly namespace scoped (e.g. `json::object`, `json::value`) and never referred to as bare (e.g. `object`, `value`).  
  
Currently most of the docs use bare names but scoped names are used in a few places (notably the ones that are imported from the examples cpp files).  
  
The scoped names are preferable over the bare names because the bare names are too generic (especially ones that collide with std or boost types, such as `string_view`).  Someone copy-pasting code from examples will end up with something that doesn't compile unless they `using namespace boost::json;`, which I hope we can all agree is a bad practice that shouldn't be encouraged.  
  
I don't think the full `boost::json` namespace needs to be spelled out; `json` by itself is sufficient indication of scope and it's not a bad practice to assume that the user has aliased that namespace (although it wouldn't hurt to explicitly give an example of that in the Quick Look).  
  
It is especially important for the examples around `tag_invoke`, as that is a mixture of types from the `boost::json` namespace and the users' own namespace, which can be highly confusing if you are not completely familiar with the types provided by the library.

---

## Comment 1

> Username: Mike-Devel  
> Created at: 2020-09-30 08:34:57 UTC  
> Url: https://github.com/boostorg/json/issues/434#issuecomment-701247612  

Generally, I'd only explicitly add the namespace for things that are not in the boost::json namespace because otherwise that is essentially is just noise. W.r.t. examples however, I'd agree with your copy/paste argument.   
  
Not quite sure about `boost::json::string_view` (which is an alias for either `boost::string_view` or `std::string_view`).

---

## Comment 2

> Username: uecasm  
> Created at: 2020-09-30 09:28:29 UTC  
> Url: https://github.com/boostorg/json/issues/434#issuecomment-701275779  

Especially with a prefix as short as `json`, I think that the clarity of specifying it wins against not doing so due to noise.  
  
(But I do just mean in any code-block-style section, regardless of whether that came from a cpp include or not.  I agree that in most cases it would be noise without benefit in descriptive text not intended to be copy-pasted.)  
  
Regarding `json::string_view` (and `json::error_code` too), I do think it should be scoped as such precisely because it can be brought in from either `std` or `boost`, thus the `json` scoped name is the only one actually guaranteed to compile regardless.  It may introduce some slight confusion ("is this a different type from `std::string_view`?"... but since the answer is sometimes yes and sometimes no, it's a good thing that it might make someone pause and check before making assumptions).

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2020-09-30 10:45:50 UTC  
> Url: https://github.com/boostorg/json/issues/434#issuecomment-701312532  

> Especially with a prefix as short as json, I think that the clarity of specifying it wins against not doing so due to noise.  
  
I think I didn't read your initial post properly. I was thinking about a `boost::json::` prefix not just `json::`.  
  
> Regarding json::string_view (and json::error_code too), I do think it should be scoped as such precisely because it can be brought in from either std or boost, thus the json scoped name is the only one actually guaranteed to compile regardless.  
  
For `string_view`, I'm not sure, if the user ever has a reason to write that in his code, but for error_code I think you argument is admittedly very valid. Probably also for `string`, which is never a `std::string`.  
  
After having another look at some more of the documentation pages, I think I'm convinced, that at least a consistent `json::` prefix would be a good idea.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-04 00:16:17 UTC  
> Url: https://github.com/boostorg/json/issues/434#issuecomment-703180387  

I see your point but this would look absolutely terrible, we would have `json::` or even worse `boost::json::` repeated ad nauseum throughout the code snippets, of which they are many. Aesthetically this is not a good look. I think the `using namespace`, while not perfect, is the lesser evil.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-03-04 05:32:41 UTC  
> Url: https://github.com/boostorg/json/issues/434#issuecomment-790305330  

I tried this locally and there was a lot of repetition of the fully qualified namespace. I think in this case it is better to have clean looking docs for the 99% of users which will understand that a "using namespace" directive is in effect, than it is to cater to that 1% at the expense of everyone else.
