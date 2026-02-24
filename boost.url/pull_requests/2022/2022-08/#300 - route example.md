# #300 route example [Closed]

> Username: alandefreitas  
> Created at: 2022-08-02 18:45:56 UTC  
> Updated at: 2022-08-03 21:05:53 UTC  
> Closed at: 2022-08-03 00:47:20 UTC  
> Url: https://github.com/boostorg/url/pull/300  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 18:47:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059265284  

📁 example/route/route.cpp

```diff
  47 |+         urls::segments prefix_segs = prefix_.segments();
  48 |+ 
  49 |+         // Match the prefix segments
```

> Username: vinniefalco  
> Created_at: 2022-08-02 18:47:37 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935918977  

Should we roll this up into a function? It looks like something people would want to use

> Username: alandefreitas  
> Created_at: 2022-08-02 18:50:33 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935922049  

The whole `match` function? As a public function or a function in the example?

> Username: vinniefalco  
> Created_at: 2022-08-02 19:27:37 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935952151  

as a separate function in the example - up to you. it looks nice. It could take 2 segment views as params? idk


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 18:48:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059265693  

📁 example/route/route.cpp

```diff
  41 |+     {
  42 |+         // Target segments
  43 |+         urls::static_pool<1024> pool;
```

> Username: vinniefalco  
> Created_at: 2022-08-02 18:48:01 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935919407  

unused variable?

> Username: alandefreitas  
> Created_at: 2022-08-02 18:50:51 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935922325  

Oh... I didn't get a warning. I have to check this.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 18:49:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059266905  

📁 example/route/route.cpp

```diff
 104 |+     if (!target_r)
 105 |+     {
 106 |+         std::cerr
```

> Username: vinniefalco  
> Created_at: 2022-08-02 18:49:08 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935920596  

This looks noisy, maybe we should use the exceptions?

> Username: alandefreitas  
> Created_at: 2022-08-02 18:51:34 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935923059  

And just let it throw?

> Username: vinniefalco  
> Created_at: 2022-08-02 21:24:01 UTC  
> Updated_at: 2022-08-02 21:24:02 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r936039086  

Yeah, why not" Just put one big `try` inside `main` and report it that way.


---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-08-02 18:49:10 UTC  
> Url: https://github.com/boostorg/url/pull/300#issuecomment-1203091033  

The only thing bothering me here is that `route` is not a route at all. At least not in the common sense of the word.  
  
In other libraries, this process of searching a corresponding file is usually considered a "middleware" we use as a fallback when we _can't_ find a route.  
  
For instance,  
  
https://github.com/expressjs/express/blob/2c47827053233e707536019a15499ccf5496dc9d/examples/static-files/index.js#L22

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 18:49:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059267445  

📁 example/route/route.cpp

```diff
 132 |+         std::cout
 133 |+             << target << " would match the file "
 134 |+             << result << std::endl;
```

> Username: vinniefalco  
> Created_at: 2022-08-02 18:49:34 UTC  
> Updated_at: 2022-08-02 18:49:35 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935921074  

If there's a match don't you want to show the result??


---

## Review 6 [Commented]

> Username: alandefreitas  
> Created_at: 2022-08-02 19:57:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059354433  

📁 example/route/route.cpp

```diff
 101 |+         if (match_prefix(
 102 |+                 target.segments(),
 103 |+                 static_cast<urls::url_view>(prefix_).segments()))
```

> Username: alandefreitas  
> Created_at: 2022-08-02 19:57:42 UTC  
> Updated_at: 2022-08-02 19:57:43 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935975210  

That's kind of ugly. We probably have to do something about it.   
  
Requiring `urls::segments` in the function when it only needs and is able to work with `urls::segments_view` doesn't look good.  
  
Some kind of `u.csegments()` to call the constant version of the function, like `cbegin()`/`cend()`? Like `url` and `url_view`, could `segments` maybe inherit from `segments_view`? Or maybe just making `segments` implicitly convertible to `segments_view`?

> Username: vinniefalco  
> Created_at: 2022-08-02 20:03:05 UTC  
> Updated_at: 2022-08-02 20:03:06 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935979332  

This is ugly but lets just merge this thing and move forward, it doesn't have to be perfect for now. Open an issue!


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 20:00:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059357606  

📁 example/route/route.cpp

```diff
  35 |+ bool match_prefix(
  36 |+     urls::segments_view target,
  37 |+     urls::segments_view prefix)
```

> Username: vinniefalco  
> Created_at: 2022-08-02 20:00:36 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935977425  

should we trivially reject when `target.size() < prefix.size()` ?


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 20:01:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059359071  

📁 example/route/route.cpp

```diff
  93 |+         @param target Target URL path
  94 |+         @param result Store the corresponding file
  95 |+         @return True if target matches the directory
```

> Username: vinniefalco  
> Created_at: 2022-08-02 20:01:57 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935978434  

`True` is not capitalized, this is a keyword


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-02 20:02:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/300#pullrequestreview-1059359386  

📁 example/route/route.cpp

```diff
  92 |+ 
  93 |+         @param target Target URL path
  94 |+         @param result Store the corresponding file
```

> Username: vinniefalco  
> Created_at: 2022-08-02 20:02:15 UTC  
> Url: https://github.com/boostorg/url/pull/300#discussion_r935978682  

`@param result An out-parameter holding the resulting path`


---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-08-02 21:57:20 UTC  
> Url: https://github.com/boostorg/url/pull/300#issuecomment-1203253441  

> The only thing bothering me here is that route is not a route at all. At least not in the common sense of the word.  
  
Its close enough for the review, and we can change it before the first Boost release. We need to get this in as soon as possible and then move on to the other things.

---
