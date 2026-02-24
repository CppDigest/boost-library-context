# #39 - Documentation: Make proxy_iterator_interface more prominent [Closed]

> Username: mrks  
> Created at: 2020-04-25 20:40:47 UTC  
> Updated at: 2020-06-24 02:50:12 UTC  
> Closed at: 2020-06-24 02:50:12 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/39  

First of all, thank you a lot for this. Finally, we might be able to fix our [iterators being incremented one-by-one](https://github.com/hyrise/hyrise/issues/1531) for things like lower_bound. I have posted a small example on why this makes a difference [here](https://github.com/hyrise/hyrise/issues/1531#issuecomment-619403434). Feel free to use it for whatever.  
  
After having spent a couple of hours tracking down wrong results with `->`, I wonder if `proxy_iterator_interface` could be documented more prominently. Here's a suggestion for README.md, to be placed somewhere at the bottom:  
  
> This also allows for proxy iterators that correctly skip entries when `std::advance` is called; allowing for efficient executions of, e.g., `std::lower_bound`. For this, use `proxy_iterator_interface`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2020-05-02 02:59:48 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/39#issuecomment-622659230  

That result with `increment()` being called over and over is bananas.  
  
As for the need to use `proxy_iterator_interface`, there is none, at least not in the general case.  Random access and proxy-ness are orthogonal.  If your `value_type` is `int`, you may well want `int &` as the `reference_type` for your iterator -- that is, you may well *not* want a proxy iterator at all.

---

## Comment 2

> Username: mrks  
> Created at: 2020-05-04 18:19:06 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/39#issuecomment-623624845  

First of all, congratulations on moving this into boost. :)  
  
> That result with increment() being called over and over is bananas.  
  
Could you clarify "bananas"? My understanding is that this is the expected pre-C++-20 behavior because iterator::reference is not `int&`, making it a non-LegacyForwardIterator. Is that legacy requirement what you consider to be bananas? Using `iterator_interface` instead of `iterator_facade` is C++20-compatible and is expected to fix this, right?  
  
> As for the need to use proxy_iterator_interface, there is none, at least not in the general case. Random access and proxy-ness are orthogonal. If your value_type is int, you may well want int & as the reference_type for your iterator -- that is, you may well not want a proxy iterator at all.  
  
Agreed. My text suggestion up there was wrong in that regard.  
  
The proxy issue can be seen [here](https://gist.github.com/mrks/6f182909e83bf07f36bbb29fc58d05fa). With the old `iterator_facade`, which didn't have a `Pointer` template argument, `dereference()` worked for both proxy and non-proxy values. As I expect more than just us using this as a replacement for `iterator_facade`, I believe that a prominent one-line comment to the difference between `iterator_interface` and `proxy_iterator_interface` could save people some time.

---

## Comment 3

> Username: tzlaine  
> Created at: 2020-05-04 18:30:09 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/39#issuecomment-623630552  

It's bananas that something called a `random_access_iterator` is moving like a forward iterator.  And yes, that's a problem with how C++17-and-prior proxy iterators were specified.  
  
As for the other part, I see what you mean.  My assumption was that if you need to use `proxy_iterator_interface`, you already know what a proxy iterator is.  That is not a safe assumption to make, and not in keeping with how Boost libraries are best documented.  I'll update the docs.
