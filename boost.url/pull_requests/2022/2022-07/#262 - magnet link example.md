# #262 magnet link example [Closed]

> Username: alandefreitas  
> Created at: 2022-07-28 02:12:50 UTC  
> Updated at: 2022-08-03 21:06:25 UTC  
> Closed at: 2022-08-02 22:19:56 UTC  
> Url: https://github.com/boostorg/url/pull/262  

fix #248

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-07-28 02:19:57 UTC  
> Url: https://github.com/boostorg/url/pull/262#issuecomment-1197575549  

An automated preview of the documentation is available at [https://262.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://262.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-07-30 01:34:56 UTC  
> Url: https://github.com/boostorg/url/pull/262#issuecomment-1200060758  

An automated preview of the documentation is available at [https://262.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://262.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:19:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056302154  

📁 example/Jamfile

```diff
   8 |+ #
   9 |+ 
  10 |+ build-project grammar ;
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:19:38 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933804718  

What is the "grammar" project?

> Username: alandefreitas  
> Created_at: 2022-08-01 15:57:01 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934684777  

Just the targets in `example/grammar`. Should this "`add_subdirectory`" be expressed differently?

> Username: vinniefalco  
> Created_at: 2022-08-01 19:19:34 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934840488  

I think "grammar" should be renamed to "magnet"


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:20:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056302790  

📁 example/grammar/magnet.cpp

```diff
 146 |+                 return false;
 147 |+             error_code ec;
 148 |+             char buf_[1024];
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:20:51 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933804816  

eww... a hardcoded limit? Not feeling it..

> Username: alandefreitas  
> Created_at: 2022-08-01 16:03:00 UTC  
> Updated_at: 2022-08-01 16:03:01 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934690300  

Yes. This is the least bad solution I found for something peculiar about the syntax. We have to check if this is a valid URL. The problem is the URL is percent-encoded twice.   
  
So `parse_uri(string_view)` doesn't work and `parse_uri(pct_encoded_view)` doesn't exist. `parse_uri(pct_encoded_view)` doesn't even make sense because it can't even refer to anything.   
  
The only complete solution would be another rule for a "url_view" that would point to a string that has been percent-encoded twice, but that's not worth the example.

> Username: alandefreitas  
> Created_at: 2022-08-01 16:22:43 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934707429  

Another solution would be just to not check if the value is a valid url. Then the "user" is responsible for doing that.

> Username: vinniefalco  
> Created_at: 2022-08-01 17:14:13 UTC  
> Updated_at: 2022-08-01 17:14:14 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934748122  

We could require that the user pass in a _MutableString_ as a temporary buffer used to first hold the percent-decoded string, and then in the call to `parse_uri`. The user would receive a `url_view` which points to the mutable string buffer. The user can then decide if they want to keep the url view, or if they want to construct an owning `url` from it.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:21:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056303718  

📁 example/grammar/magnet.cpp

```diff
  15 |+ // A small filter and transform view
  16 |+ template <class C, class V, class P, class T>
  17 |+ class filter_view
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:21:45 UTC  
> Updated_at: 2022-07-30 13:21:46 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933804954  

Hmm I think this needs to be broken up into individual .hpp files

> Username: alandefreitas  
> Created_at: 2022-08-01 16:03:32 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934690778  

Can we do that? I thought examples were supposed to be in a single file.

> Username: vinniefalco  
> Created_at: 2022-08-01 17:15:50 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934749267  

As an author you can exercise editorial discretion on whether or not to break things up. A simple HTTP client or server is nice to have as one file that you can just copy. But you're creating a robust vocabulary component with additional utility classes that could be useful on their own (e.g. `filter_view`). In this case I think multiple files are warranted. But its up to you.

> Username: alandefreitas  
> Created_at: 2022-08-01 19:28:48 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934846801  

Mmm... OK. I saw Boost.Beast examples are huge. So I thought it was kind of a rule not to split examples into multiple files. No problem then.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:22:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056303808  

📁 example/grammar/magnet.cpp

```diff
  34 |+ 
  35 |+         void
  36 |+         increment_false()
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:22:29 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805010  

need a better name for this, and a comment explaining what it does


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:23:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056303852  

📁 example/grammar/magnet.cpp

```diff
  14 |+ 
  15 |+ // A small filter and transform view
  16 |+ template <class C, class V, class P, class T>
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:23:06 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805093  

This needs a better javadoc with explanations for the template parameters. And maybe better names.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:23:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056303873  

📁 example/grammar/magnet.cpp

```diff
 114 |+ 
 115 |+ // A new url type for magnet links
 116 |+ class magnet_link_view
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:23:28 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805126  

need a real javadoc for this


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:24:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056303936  

📁 example/grammar/magnet.cpp

```diff
 118 |+     url_view u_;
 119 |+ 
 120 |+     struct is_exact_topic {
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:24:16 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805202  

I like the way this is implemented! However..  I think it would be better to separate the function definitions from the declaration. This will make it easier to understand the synopsis of the class.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:24:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056304141  

📁 example/grammar/magnet.cpp

```diff
 121 |+         bool operator()(query_param_encoded_view p)
 122 |+         {
 123 |+             if (p.key == "xt")
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:24:39 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805244  

A comment would be nice here explaining that it is using the lazy op== (or is it?)

> Username: alandefreitas  
> Created_at: 2022-08-01 16:40:41 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934722439  

I changed it to `query_param_view` so we can use the proper `pct_encoded_view` and `op==`. `query_param_view` is also the right thing to do because these things are allowed to be encoded.  
  
I ended up using `query_param_encoded_view` because things were becoming inconvenient with `pct_encoded_view` since it doesn't have some functions like `starts_with`, etc...


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:25:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056304864  

📁 example/grammar/magnet.cpp

```diff
 134 |+     };
 135 |+ 
 136 |+     class is_url_with_key {
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:25:44 UTC  
> Updated_at: 2022-07-30 13:25:45 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805350  

You have to explain what this is, and I think this declaration needs to be outside the declaration of the enclosing class (use a forward declaration here).  And consider making this not a nested type.


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:26:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056307366  

📁 example/grammar/magnet.cpp

```diff
 200 |+ public:
 201 |+     using topics_view =
 202 |+         filter_view<params_encoded_view, url_view, is_exact_topic, to_url>;
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:26:40 UTC  
> Updated_at: 2022-07-30 13:26:41 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805535  

`filtered_view`


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:27:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056308487  

📁 example/grammar/magnet.cpp

```diff
 220 |+ 
 221 |+     // info hash of the file or files
 222 |+     infohashes_view
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:27:05 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805575  

`info_hashes_view`?


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:27:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056310495  

📁 example/grammar/magnet.cpp

```diff
 290 |+     // informal options / parameters
 291 |+     optional<pct_encoded_view>
 292 |+     param(string_view key) const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:27:58 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933805921  

These all need javadocs and separated function definitions


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:28:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056310513  

📁 example/grammar/magnet.cpp

```diff
 298 |+         {
 299 |+             query_param_encoded_view p = *it;
 300 |+             if (p.key.starts_with("x.") &&
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:28:12 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806004  

What if the key is percent-encoded?

> Username: alandefreitas  
> Created_at: 2022-08-01 19:18:56 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934840039  

Yes. As in https://github.com/CPPAlliance/url/pull/262#discussion_r934722439, not having these helpers in pct_encoded_view makes it a little inconvenient. But I'll fix it and just do it the hard way.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:28:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056310536  

📁 example/grammar/magnet.cpp

```diff
 317 |+     // get a parameter as a pct_encoded_view
 318 |+     optional<pct_encoded_view>
 319 |+     decode_param(string_view key) const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:28:31 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806136  

`decoded_param`

> Username: vinniefalco  
> Created_at: 2022-08-01 17:16:18 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934749606  

I guess if its private, I don't care so much about the name.


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:29:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056310742  

📁 example/grammar/magnet.cpp

```diff
 353 |+     // https://www.bittorrent.org/beps/bep_0053.html
 354 |+     // https://github.com/webtorrent/magnet-uri
 355 |+     // https://en.wikipedia.org/wiki/Magnet_URI_scheme
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:29:32 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806303  

You need a real javadoc for this, look at the format I used for `grammar`


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:30:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056311745  

📁 example/grammar/magnet.cpp

```diff
 365 |+         if(!rrv)
 366 |+         {
 367 |+             it = it0;
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:30:40 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806413  

Rules don't have to rewind, this responsibility is on the caller

> Username: alandefreitas  
> Created_at: 2022-08-01 19:21:55 UTC  
> Updated_at: 2022-08-01 19:21:56 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934841986  

This is not for the user. The parser needs to rewind because it validates if it starts with a valid `absolute_uri` in the first step so it knows where this uri ends. Then we really parse the `parse_absolute_uri` in the second step. That's because the rules didn't return `url_view` yet.

> Username: vinniefalco  
> Created_at: 2022-08-01 19:22:53 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934842710  

ah! nice :)


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:31:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056312633  

📁 example/grammar/magnet.cpp

```diff
 371 |+         // 2) Parse this url
 372 |+         magnet_link_view m;
 373 |+         m.u_ = *parse_absolute_uri({it0, it});
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:31:06 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806485  

Why are we parsing it twice?

> Username: alandefreitas  
> Created_at: 2022-08-01 20:23:19 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934883365  

Because the rules didn't return url_view before.


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:31:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056314940  

📁 example/grammar/magnet.cpp

```diff
 379 |+         auto pend = ps.end();
 380 |+         auto f = magnet_link_view::is_exact_topic{};
 381 |+         pit = std::find_if(pit, pend, f);
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:31:59 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806609  

just pass `magnet_link_view::is_exact_topic()` as an argument, no need to name it.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:32:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056316177  

📁 example/grammar/magnet.cpp

```diff
 391 |+         {
 392 |+             if (!magnet_link_view::is_exact_topic{}(p))
 393 |+                 return true;
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:32:30 UTC  
> Updated_at: 2022-07-30 13:32:31 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933806784  

wait how can you return true, shouldn't this be an error code?

> Username: alandefreitas  
> Created_at: 2022-08-01 20:24:31 UTC  
> Updated_at: 2022-08-01 20:24:32 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934884301  

Nop... We are checking if all exact topics are valid urls. All other params are valid.


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:33:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056316603  

📁 example/grammar/magnet.cpp

```diff
  11 |+ #include <iostream>
  12 |+ 
  13 |+ using namespace boost::urls;
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:33:33 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933807156  

hmm idk about this, we might need a namespace alias and `url::` or `urls::` in front of everything

> Username: alandefreitas  
> Created_at: 2022-08-01 20:25:27 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934884985  

I thought https://github.com/CPPAlliance/url/issues/241 applied here


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:34:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056316658  

📁 example/grammar/magnet.cpp

```diff
 429 |+                                             "&tr=udp%3A%2F%2Ftracker.example3.com%3A6969"
 430 |+                                             "&tr=udp%3A%2F%2Ftracker.example2.com%3A80"
 431 |+                                             "&tr=udp%3A%2F%2Ftracker.example1.com%3A1337\n";
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:34:19 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933807405  

Are these links to copyrighted material?

> Username: alandefreitas  
> Created_at: 2022-08-01 20:35:59 UTC  
> Updated_at: 2022-08-01 20:36:00 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934899900  

Nop...  
  
display name: Leaves of Grass by Walt Whitman.epub  
  
Leaves of grass by Whitman, Walt, 1819-1892  
  
https://archive.org/search.php?query=creator%3A%22Whitman%2C+Walt%2C+1819-1892%22


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:35:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056316720  

📁 example/grammar/magnet.cpp

```diff
 442 |+ 
 443 |+     auto xt = m.exact_topics();
 444 |+     for (auto it = xt.begin(); it != xt.end(); ++it)
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:35:05 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933807488  

Why not  
```  
for( auto t : m.exact_topics() )  
```  
?


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-07-30 13:35:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1056316748  

📁 example/grammar/magnet.cpp

```diff
 494 |+ 
 495 |+     return EXIT_SUCCESS;
 496 |+ }
```

> Username: vinniefalco  
> Created_at: 2022-07-30 13:35:28 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r933807507  

missing newline


---

## Comment 26

> Username: vinniefalco  
> Created_at: 2022-07-30 13:36:14 UTC  
> Url: https://github.com/boostorg/url/pull/262#issuecomment-1200159273  

Make sure that CI is building this. If it passes, then you might as well merge it right away since having something is strictly better than having nothing, even though there is more work we need to do on it.

---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-01 20:45:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/262#pullrequestreview-1057843172  

📁 example/magnet/CMakeLists.txt

```diff
  17 |+ 
  18 |+ set_property(TARGET magnet PROPERTY FOLDER "example")
  19 |+ target_link_libraries(magnet PRIVATE Boost::url)
```

> Username: vinniefalco  
> Created_at: 2022-08-01 20:45:36 UTC  
> Url: https://github.com/boostorg/url/pull/262#discussion_r934906290  

missing newline


---
