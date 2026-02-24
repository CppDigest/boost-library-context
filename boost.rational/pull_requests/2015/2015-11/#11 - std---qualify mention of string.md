# #11 std::-qualify mention of string [Closed]

> Username: geoffromer  
> Created at: 2015-11-19 19:47:54 UTC  
> Updated at: 2015-11-30 20:35:22 UTC  
> Closed at: 2015-11-30 20:35:21 UTC  
> Url: https://github.com/boostorg/rational/pull/11  

Even with `using namespace std`, an unqualified mention of `string` is ambiguous if the user has defined `string` as a name in the global namespace.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-11-19 19:52:07 UTC  
> Url: https://github.com/boostorg/rational/pull/11#issuecomment-158175068  

Gromer, what about the `streamsize` directly below? Shouldn't all the `std` bits have to be qualified?

---

## Comment 2

> Username: geoffromer  
> Created_at: 2015-11-19 19:54:37 UTC  
> Url: https://github.com/boostorg/rational/pull/11#issuecomment-158176387  

Yes, they should, although pragmatically they're less likely to cause problems. I'm happy to do it that way if you prefer.

---

## Comment 3

> Username: mclow  
> Created_at: 2015-11-30 16:52:48 UTC  
> Url: https://github.com/boostorg/rational/pull/11#issuecomment-160687671  

I guess what I'm asking is "why is string special here?". I'm guessing that this is not a random change; that somewhere, this caused some trouble.

---

## Comment 4

> Username: geoffromer  
> Created_at: 2015-11-30 17:47:22 UTC  
> Url: https://github.com/boostorg/rational/pull/11#issuecomment-160703845  

In practice, unqualified `string` causes problems for us because we have a global `::string` in our codebase, whereas we don't have a global `::streamsize`. Note that our `::string` isn't an alias for `std::string`, but a distinct type (if it was an alias, there would be no problem). It's an alternative to libstdc++'s `std::string` that uses the small string optimization and isn't copy-on-write, and therefore performs better and conforms to the standard better.

---

## Comment 5

> Username: mclow  
> Created_at: 2015-11-30 18:22:05 UTC  
> Url: https://github.com/boostorg/rational/pull/11#issuecomment-160713799  

that's what I figured.  I would be happier with this patch if the `using namespace std;` were to go away, and all the std bits were explicitly qualified.  If you don't want to do that, I'll do it.

---

## Comment 6

> Username: geoffromer  
> Created_at: 2015-11-30 20:35:21 UTC  
> Url: https://github.com/boostorg/rational/pull/11#issuecomment-160753830  

Sure, but that's a bigger change than I'm comfortable making in the GitHub web editor, so I've started over with #12.

---
