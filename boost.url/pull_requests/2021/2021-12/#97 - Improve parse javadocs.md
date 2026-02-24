# #97 Improve parse javadocs [Merged]

> Username: alandefreitas  
> Created at: 2021-12-31 23:15:57 UTC  
> Updated at: 2022-01-05 01:07:09 UTC  
> Merged at: 2022-01-05 01:07:09 UTC  
> Closed at: 2022-01-05 01:07:09 UTC  
> Url: https://github.com/boostorg/url/pull/97  

This PR  
  
- fixes a number doxygen warnings and errors  
- simplifies the documentation so that docca generates better output  
- removes any commands docca cannot render  
- includes an exposition for the grammar rule objects  
- simplifies the examples  
- adjusts the documentation to refer to the grammar namespace  
- simplifies descriptions by removing unnecessary and excessive mathematical symbolism  
- removes details that are implied from the exposition  
- updates parameters to use stock sentences  
- removes BOOST_URL_DOCS from overloads and adjusts documentation accordingly

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:33:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842952304  

📁 include/boost/url/bnf/parse.hpp

```diff
  17 | #include <type_traits>
  18 | 
  19 |+ /** @file
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:32:56 UTC  
> Updated_at: 2022-01-03 18:33:01 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777639003  

This looks like a javadoc attached to the file? How can I view this in the documentation output?

> Username: alandefreitas  
> Created_at: 2022-01-03 18:45:17 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777644899  

Yes. This is the official way of doing it in doxygen. If you generate the doxygen output, this will be in the file documentation. Doxygen understands it 100%.   
  
Whether docca is currently able to understand it is another issue we can handle separately, maybe by moving this content somewhere else. In any case, neither doxygen nor docca would understand what we had before.

> Username: vinniefalco  
> Created_at: 2022-01-03 19:15:29 UTC  
> Updated_at: 2022-01-03 19:16:04 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777657986  

Okay but the number one rule of writing documentation is that it has to be visible in the output. Otherwise there is nothing to read, and nothing to review. When you write javadocs you are supposed to generate the documentation and observe that the output matches what you expect. How did you do that for this comment block?

> Username: alandefreitas  
> Created_at: 2022-01-03 20:20:05 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777685637  

You're 100% correct. That's what I always do too.   
  
In the general case, the problem here is sometimes we have some conflict between what the correct javadocs/doxygen are and what docca is capable of processing. It might be useful to have an official guideline in this case or for each case.   
  
In this particular case, for instance, we just had a comment describing how parsing works, which failed to be to be visible in the output either way. I improved that with the proper doxygen command for that, which doxygen parses correctly of course, but docca doesn't allow it to be visible in the output. There is the visible doxygen output:  
  
![image](https://user-images.githubusercontent.com/5369819/147975680-34ff2482-3144-4241-a5a6-d62f4de3bcf4.png)  
  
So in that case, as none of the options would be visible in the output anyway, I inferred that it would be better to have the correct javadocs already instead of making the maintaining wrong javadocs that both docca and doxygen can't parse anyway. At least, in the first alternative, it would be visible output once docca catches up and it's visible for anyone who runs doxygen on the source files. Or we can have an intermediary solution that is replicating this content somewhere else until docca catches up.   
  
This is not the only place where this happens. In each case, we might decide to use proper documentation until docca catches up or do the opposite and rewrite everything once docca catches up. There are short-term and long-term pros and cons to each approach.

> Username: vinniefalco  
> Created_at: 2022-01-03 20:23:54 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777687115  

No one runs doxygen and produces the HTML output, so these javadocs are unobservable and untestable. If you want to have this information you need to figure out a good way to present it. I would put it in the exposition rather than trying to make it a javadoc.

> Username: alandefreitas  
> Created_at: 2022-01-03 20:39:46 UTC  
> Updated_at: 2022-01-03 20:39:47 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777693523  

> No one runs doxygen and produces the HTML output, so these javadocs are unobservable and untestable  
  
Yes. So is what we had before? Should it be removed then?

> Username: alandefreitas  
> Created_at: 2022-01-03 20:42:53 UTC  
> Updated_at: 2022-01-03 20:42:54 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777694695  

>  I would put it in the exposition rather than trying to make it a javadoc.  
  
A good place would be a future `bnf_element` (or something) concept, but I don't even think there's a canonical way to document these yet.

> Username: alandefreitas  
> Created_at: 2022-01-03 20:45:31 UTC  
> Updated_at: 2022-01-03 20:47:08 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777695757  

> No one runs doxygen and produces the HTML output  
  
Yes. I just showed you the doxygen output because it justifies the rationale for what I discussed later. We do have a problem with different capabilities between doxygen and docca.

> Username: vinniefalco  
> Created_at: 2022-01-03 20:51:42 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777698201  

> We do have a problem with different capabilities between doxygen and docca.  
  
There's no problem. Javadocs in source code must be limited to what docca supports. This has worked for Beast, StaticString, JSON.... and now URL

> Username: alandefreitas  
> Created_at: 2022-01-03 20:57:06 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777700379  

> There's no problem.   
  
OK.   
  
> Javadocs in source code must be limited to what docca supports  
  
OK. I get your point. I understand things like not being able to document files or group class members as a problem. But that's OK. I understand the point.

> Username: vinniefalco  
> Created_at: 2022-01-03 21:22:13 UTC  
> Updated_at: 2022-01-03 21:22:14 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777710349  

This is still not fixed

> Username: alandefreitas  
> Created_at: 2022-01-03 23:19:16 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777750007  

I know. The discussion is happening here: https://github.com/CPPAlliance/url/pull/97#discussion_r777702510


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:34:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842953274  

📁 include/boost/url/bnf/parse.hpp

```diff
  69 | 
  59 |-    @return `true` if the first char in the range matches @ref ch successfully,
  70 |+    Never throws
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:34:31 UTC  
> Updated_at: 2022-01-03 18:34:32 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777639688  

"Throws nothing" is the canonical text


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:35:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842953937  

📁 include/boost/url/bnf/parse.hpp

```diff
  74 |+    [Backus–Naur form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form),
  75 |+ 
  76 |+    @ref boost::urls::error
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:35:33 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777640178  

Prefer `@ref error`. There's no need to write out the namespace. However, note that there are two enumerations in this library. One in `boost::urls::error` and the other in `boost::urls::bnf::error`. Because `urls::bnf` is logically a small, separate "mini-library." That is, a dependent library can use `bnf` algorithms without using the `urls` algorithms.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:36:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842954356  

📁 include/boost/url/bnf/parse.hpp

```diff
  86 | 
  68 |-    @param[out] ec The error code this function should set @ref if it fails
  87 |+    @param ec The error code this function should set `it` it fails
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:36:05 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777640503  

The canonical text is  
```  
@param ec Set to the error, if any occurred.  
```


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:36:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842954829  

📁 include/boost/url/bnf/parse.hpp

```diff
  98 | 
  80 |- /** @brief Parse a sequence of elements
  99 |+ /** Parse beginning of a char range as a sequence of arbitrary BNF elements
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:36:53 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777640881  

I usually make the brief (first line of javadoc) generic. For example "Parse a substring into one or more BNF elements"

> Username: alandefreitas  
> Created_at: 2022-01-03 18:58:36 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777650586  

OK. Changing it back.  
  
There are two components in that. For the second component, "one or more BNF elements" and "one or more BNF elements" are almost synonymous.   
  
As for the first component in "Parse a substring into one or more BNF elements" is similar to something I had before (but after "Parse a sequence of elements"). The reason I changed it was because it looked ambiguous when I compared it with other overloads. For instance, "Parse a substring into one or more BNF elements" could also apply to `parse_string`, because a `string_view` is also potentially a substring.   
  
As both a string_view and two pointers represent a subrange/substring, it seemed like the relevant difference between these overloads was whether they consider the whole sequence or only the beginning of the sequence.  
  
I'm changing it back but, just in case, that was the rationale.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:38:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842955948  

📁 include/boost/url/bnf/parse.hpp

```diff
 106 |+    corresponding function overload of `parse`
 107 |+    `bool parse(char const*& it, char const* const end, error_code&, Arg_i const& arg_i)`
 108 |+    should be defined to consume the input from [`it`, `end`) into `arg_i`.<br>
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:38:55 UTC  
> Updated_at: 2022-01-03 18:38:56 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777641744  

This is way too technical and jargony. It needs to be written in a simpler fashion that ordinary users who don't read wg21 papers can grok.

> Username: alandefreitas  
> Created_at: 2022-01-03 19:10:34 UTC  
> Updated_at: 2022-01-03 19:10:35 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777655824  

Right. I usually find it less ambiguous and easier to read something closer to formal mathematical symbolism but you're correct. Not many people will like that.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:39:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842956318  

📁 include/boost/url/bnf/parse.hpp

```diff
  96 |-    the function returns `false` and sets the error number in @ref ec to the
 112 |+    the function returns `false` and sets the error number in `ec` to the
 113 |    appropriate error number defined in @ref boost::urls::error.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:39:35 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777642055  

You should assume that people reading the docs know how `error_code` works. You don't have to tell them that it is set to a number when an error happens, and cleared on success.

> Username: alandefreitas  
> Created_at: 2022-01-03 19:12:07 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777656521  

Oh... right. I did fix that for the other functions but then missed this one.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:40:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842957504  

📁 include/boost/url/bnf/parse.hpp

```diff
 167 |-      }
 168 |-      return 0;
 117 |+    if (bnf::parse(it, end, ec, bnf_element_1, bnf_element_2, bnf_element_3)) {
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:40:58 UTC  
> Updated_at: 2022-01-03 18:40:59 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777642906  

The correct usage is  
```  
using bnf::parse;  
```  
  
followed by calling `parse` without namespace qualification (like `swap`).

> Username: vinniefalco  
> Created_at: 2022-01-03 20:53:51 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777699108  

> without namespace qualification

> Username: vinniefalco  
> Created_at: 2022-01-03 21:28:25 UTC  
> Updated_at: 2022-01-03 21:28:26 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777712939  

I still see namespace qualification

> Username: alandefreitas  
> Created_at: 2022-01-03 23:28:22 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777752535  

OK. I thought the customization point was only expected for the `char const*&, char const*, error_code&, T` overload, and the `bnf::parse` overloads were just entry points.

> Username: vinniefalco  
> Created_at: 2022-01-03 23:30:10 UTC  
> Updated_at: 2022-01-03 23:30:11 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777753003  

it is easier to teach if we always write it uniformly


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:41:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842957848  

📁 include/boost/url/bnf/parse.hpp

```diff
 176 |-    @see https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form
 124 |+      Internal calls to `parse` overloads for `args` may throw, although they should
 125 |+      prefer setting `ec` instead.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:41:33 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777643257  

Stock phrase: "Basic guarantee"

> Username: alandefreitas  
> Created_at: 2022-01-03 19:21:59 UTC  
> Updated_at: 2022-01-03 19:22:00 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777660912  

I'm not sure I understand how to replicate this one. The only place I can find the term `Basic guarantee` is in `url/url.hpp` so I can't infer the best approach:  
  
```  
@par Exception Safety  
Basic guarantee.  
Calls to allocate may throw.  
```  
  
Should I replace the current description or just add the `Basic guarantee` prefix?  
  
Should the `Exception Safety` paragraph only describe one of the four [Exception safety guarantees](https://en.cppreference.com/w/cpp/language/exceptions) instead of discussing it?  
  
1. Nothrow (or nofail) exception guarantee -- the function never throws exceptions. Nothrow (errors are reported by other means or concealed) is expected of destructors and other functions that may be called during stack unwinding. The destructors are noexcept by default. (since C++11) Nofail (the function always succeeds) is expected of swaps, move constructors, and other functions used by those that provide strong exception guarantee.  
2. Strong exception guarantee -- If the function throws an exception, the state of the program is rolled back to the state just before the function call. (for example, std::vector::push_back)  
3. Basic exception guarantee -- If the function throws an exception, the program is in a valid state. No resources are leaked, and all objects' invariants are intact.  
4. No exception guarantee -- If the function throws an exception, the program may not be in a valid state: resource leaks, memory corruption, or other invariant-destroying errors may have occurred.

> Username: vinniefalco  
> Created_at: 2022-01-03 19:25:39 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777662440  

This is a good stock phrase (if the function allocates)  
  
```  
@par Exception Safety  
Basic guarantee.  
Calls to allocate may throw.  
```  
  
In this case we don't know if a BNF element will allocate since the function is generic. And we don't know if a BNF element will throw. So we might use this instead:  
  
```  
@par Exception Safety  
Defined by the types of `args`.  
```  
  
or something like that.

> Username: alandefreitas  
> Created_at: 2022-01-03 20:26:55 UTC  
> Updated_at: 2022-01-03 20:26:57 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777688277  

OK. Changing to `defined by the types of args` then.   
  
So if `Defined by the types of args.` is correct, I wasn't wrong in assuming this section is for discussing exception safety in general (like cpp reference does) instead of just choosing one of the categories, right? And there's no stock phrase in that case because it's not the same logic, right?

> Username: alandefreitas  
> Created_at: 2022-01-03 20:32:19 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777690558  

Oh... by the way... I think it's possible to officially make `parse` `noexcept` depending on the types of `args` with some little metaprogramming spaghetti.

> Username: vinniefalco  
> Created_at: 2022-01-03 23:30:53 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777753173  

>  I think it's possible to officially make parse noexcept depending on the types of args with some little metaprogramming spaghetti.  
  
Maybe, but I prefer to avoid such complexity unless there is *overwhelming evidence* of significant benefit, which I am not seeing here. Simpler is usually better.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:42:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842958174  

📁 include/boost/url/bnf/parse.hpp

```diff
 141 | 
 189 |-    @param[out] ec The error code this function should set if it fails
 142 |+    @param ec The error code this function should set `it` it fails
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:42:04 UTC  
> Updated_at: 2022-01-03 18:42:05 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777643517  

stock phrase, and typo


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:42:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842958675  

📁 include/boost/url/bnf/parse.hpp

```diff
 216 |-    @ref ec is set to @ref error::leftover_input.
 172 |+    This function parses a complete string `s` into the Backus–Naur form (BNF)
 173 |+    elements [`arg_0`, `arg_1`, ...] in `args`.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:42:56 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777643899  

The important thing you need to state is "the function returns an error if some characters are left unparsed"

> Username: alandefreitas  
> Created_at: 2022-01-03 20:27:57 UTC  
> Updated_at: 2022-01-03 20:27:58 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777688735  

OK. I'm adding that. That differentiation was initially in the implicit `brief`.

> Username: vinniefalco  
> Created_at: 2022-01-03 23:31:16 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777753300  

Its hard to say it concisely in the brief


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:43:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842958759  

📁 include/boost/url/bnf/parse.hpp

```diff
 229 |-    @see @ref boost::urls::error
 185 |+      Internal calls to `parse` overloads may throw, although they should
 186 |+      prefer setting `ec` instead.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:43:04 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777643952  

stock phrase


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:45:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842959844  

📁 include/boost/url/bnf/parse.hpp

```diff
 231 |-    @return `true` if the string matches the BNF elements successfully and all
 232 |-    chars are consumed, `false` otherwise
 188 |+    @see
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:44:59 UTC  
> Updated_at: 2022-01-03 18:45:00 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777644772  

Ordering: I put `@see` at the end, and it only lists refs (`@ref`) never hyperlinks. If you want to include hyperlinks, which is fine, do it this way  
```  
@par References  
    @li <href="https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form">Backus–Naur form (Wikipedia)</a>  
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:45:27 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777644986  

Put References just before See Also. Alternatively you could use the paragraph title `@par Specification`.

> Username: alandefreitas  
> Created_at: 2022-01-03 19:26:52 UTC  
> Updated_at: 2022-01-03 19:26:53 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777662928  

OK. I must have made a mistake using the template I found in `javadoc.hpp`. It seems like the file has changed since then. I'll recreate the template.

> Username: vinniefalco  
> Created_at: 2022-01-03 19:27:45 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777663312  

javadoc.hpp will probably always be hopelessly out of date. It means well, but...

> Username: alandefreitas  
> Created_at: 2022-01-03 20:29:57 UTC  
> Updated_at: 2022-01-03 20:29:58 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777689547  

No problem. But please don't give up on it. It's still the best reference to get started because everyone has a different style.    
  
I use the bulky `///` comments in my libs. You would probably hate that too. :laughing:

> Username: vinniefalco  
> Created_at: 2022-01-03 23:31:50 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777753461  

`///` makes things harder to edit since you have to fiddle with the comments on every line

> Username: alandefreitas  
> Created_at: 2022-01-04 00:53:21 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777771677  

Yep. `///` is just an example of why `javadoc.hpp` is important.  
  
> /// makes things harder to edit since you have to fiddle with the comments on every line  
  
Not in my case. The IDE and clang-format do everything for me and it's impossible for contributors to ever get it wrong.   
  
On the other hand, the IDE always gets `/** */` and no `///` wrong for me and I always have to readjust the indentation line by line to make the text start where it should. `///` and `*` on each line, like ASIO does, works a little better though.  
  
I also find `///` (or even `///` + `*`) more aesthetically useful to identify documentation blocks when reading header files, like in ASIO.   
  
None of that is an argument, of course. Just an example related to `javadoc.hpp`.


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:45:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842960291  

📁 include/boost/url/bnf/parse.hpp

```diff
 238 |-    @param[out] t0 T0 1st BNF element
 194 |+    @return `true` if the string matches the BNF elements successfully and all
 195 |+    chars are consumed, `false` otherwise.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:45:44 UTC  
> Updated_at: 2022-01-03 18:46:00 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777645146  

You don't have to say "false otherwise". C++ users know that a `bool` is either true or false...


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:46:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842960701  

📁 include/boost/url/bnf/parse.hpp

```diff
 195 |+    chars are consumed, `false` otherwise.
 196 |+ 
 197 |+    @param s The input string this function should parse
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:46:27 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777645456  

You don't have to say "this function should parse", it is implied. Otherwise, you will be saying the same thing over and over again for each parameter and that looks awkward.


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:47:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842961242  

📁 include/boost/url/bnf/parse.hpp

```diff
 198 | 
 240 |-    @param[out] t1 T1 2nd BNF element
 199 |+    @param ec The error code this function should set if it fails. If the string is not completely consumed,
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:47:24 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777645855  

Parameter descriptions need to be short. `error::leftover_input` should be a `@ref`. I plan on changing the name. And it will be in the bnf namespace enum not the urls one.

> Username: alandefreitas  
> Created_at: 2022-01-03 19:34:36 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777666309  

Oh... I did try that, but I got:  
  
```  
/home/alandefreitas/Documents/Code/C++/boost/libs/url/include/boost/url/bnf/parse.hpp:190: warning: unable to resolve reference to `error::leftover_input' for \ref command  
```  
  
It was because the name has already been changed between the first version of the javadocs and now. It works now! :D


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 18:48:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-842961971  

📁 include/boost/url/bnf/parse.hpp

```diff
 254 | 
 278 |-    @throw system_error @ref s cannot be parsed into [@ref t0, @ref t1, ...]
 255 |+    @throw system_error `s` cannot be parsed into [`t0`, `t1`, ...]
```

> Username: vinniefalco  
> Created_at: 2022-01-03 18:48:38 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777646387  

system error throws have a stock phrase:  
```  
@throw system_error on failure  
```

> Username: vinniefalco  
> Created_at: 2022-01-03 19:26:00 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777662591  

Have a look at Asio or Beast for the exact wording


---

## Comment 18

> Username: alandefreitas  
> Created_at: 2022-01-03 20:35:36 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1004343399  

I've created another commit instead of amending or squashing so it's easier to check the changes and so you have the option to do whatever seems best in the end. But let me know if you'd rather I amended that.

---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 20:52:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843033634  

📁 include/boost/url/bnf/parse.hpp

```diff
  38 |+ 
  39 |+    These new function overloads may be defined in other namespaces. As with `std::swap`, the design relies
  40 |+    on Argument Dependent Lookup (ADL) to find these overloads.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 20:52:03 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777698350  

You have to put this in the exposition

> Username: alandefreitas  
> Created_at: 2022-01-03 21:02:05 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777702510  

Where would you recommend it to go?

> Username: vinniefalco  
> Created_at: 2022-01-03 21:23:42 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777710924  

> Where would you recommend it to go?  
  
Well, somewhere is better than nowhere. That said, let me have a look...  
  
Make a new section 4.0.BNF_parsing and dump it there. Don't forget to include the new qbk file.

> Username: vinniefalco  
> Created_at: 2022-01-03 23:28:43 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777752624  

Still not moved

> Username: alandefreitas  
> Created_at: 2022-01-03 23:46:57 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777757195  

OK then


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 20:52:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843033942  

📁 include/boost/url/bnf/parse.hpp

```diff
  60 |    std::string str = "Hello.cpp";
  61 |    auto it = str.data();
  62 |    auto const end = it + str.size();
```

> Username: vinniefalco  
> Created_at: 2022-01-03 20:52:37 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777698577  

You should usually never use `auto` in example code in docs. Specifying the correct type aids the reader in understanding.

> Username: alandefreitas  
> Created_at: 2022-01-03 21:03:17 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777703045  

Oh... yes. That was ugly...


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 20:52:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843034075  

📁 include/boost/url/bnf/parse.hpp

```diff
  88 |+    @par References
  89 |+ 
  90 |+    [Backus–Naur form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)
```

> Username: vinniefalco  
> Created_at: 2022-01-03 20:52:51 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777698683  

Where's the `@li`?

> Username: alandefreitas  
> Created_at: 2022-01-03 21:04:04 UTC  
> Updated_at: 2022-01-03 21:04:28 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777703327  

Do you always use `@li` for references and `@see` or just for references?

> Username: alandefreitas  
> Created_at: 2022-01-03 21:05:54 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777704061  

OK. Just checked it. Nevermind.

> Username: vinniefalco  
> Created_at: 2022-01-03 21:27:41 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777712673  

I use `<a href` but either way you need to put in parenthesis where the user is going, i.e.:  
```  
<a href="https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form">Backus–Naur form (Wikipedia)</a>  
```  
  
The problem though is that "BNF" doesn't really say anything. The library's API doesn't use BNF notation, nor do any of the algorithms allow you to specify BNF directly. I just use the abbreviation as a sort of proper noun to define the concept, even though actual BNF syntax is not used. You probably should never link to BNF definition anywhere in the javadocs. However you would want to link to it in the exposition.

> Username: alandefreitas  
> Created_at: 2022-01-04 00:12:22 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777762878  

> The problem though is that "BNF" doesn't really say anything.   
  
I'm not sure I understand what you mean by that.  
  
> The library's API doesn't use BNF notation, nor do any of the algorithms allow you to specify BNF directly.   
  
By "specify BNF directly" do you mean specifying it with canonical notation strings, right?  
  
> I just use the abbreviation as a sort of proper noun to define the concept, even though actual BNF syntax is not used.   
  
The library is correct to use "bnf". BNF represents much more than the canonical string notation for the derivation rules, which is just a notation.  
  
The canonical string notation for the derivation rules is not used because it's not very efficient or even worthwhile, but the whole metasyntax for the grammar and everything one needs for Chomsky's context-free grammar derivation rules are implemented by the library.   
  
Implementing a BNF syntax string parser from what we already have wouldn't even increment the library by much. It's just a matter of another recursive bnf element implementing the rule ` <symbol> ::= __expression__`.     
  
In fact, last time we talked about this a few months ago, I think my opinion was that, by looking at the reference, the library seemed to focus more on the BNF stuff than expected and that seemed to be out of scope.  
  
> You probably should never link to BNF definition anywhere in the javadocs. However you would want to link to it in the exposition  
  
OK. Done. I included that link because it seemed to be the most appropriate place for it, the core of the BNF parsing functionality, but I'm not attached to that link in any way. As a parallel discussion though, if the idea is that this library has nothing to do with BNF because it doesn't use the BNF syntax, this is probably not going to convince users as it is at the moment.

> Username: vinniefalco  
> Created_at: 2022-01-04 00:52:43 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777771549  

I just call these types "bnfs" because I couldn't think of a better name. We could change this of course.

> Username: alandefreitas  
> Created_at: 2022-01-04 01:00:57 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777773398  

You are correct to call them BNF. This is a significant part of the library. It might be dangerous to treat that as a smaller part of the library or only an informal implementation detail, given how much of the public API it represents in https://master.url.cpp.al/url/ref.html unless it were really only implementation.


---

## Review 22 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 20:54:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843035008  

📁 include/boost/url/bnf/parse.hpp

```diff
 137 |+    @par References
 138 |+ 
 139 |+    [Backus–Naur form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)
```

> Username: vinniefalco  
> Created_at: 2022-01-03 20:54:32 UTC  
> Updated_at: 2022-01-03 20:54:33 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777699382  

See: https://github.com/CPPAlliance/url/blob/02ef2ae540a10466bc7462ac4534fad2d711c627/include/boost/url/rfc/host_bnf.hpp#L32


---

## Review 23 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 20:54:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843035077  

📁 include/boost/url/bnf/parse.hpp

```diff
 134 | 
 191 |-    @param[out] args BNF elements to be parsed
 135 |+    @ref error
```

> Username: vinniefalco  
> Created_at: 2022-01-03 20:54:40 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777699432  

indent and period

> Username: vinniefalco  
> Created_at: 2022-01-03 20:55:03 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777699608  

See: https://github.com/CPPAlliance/url/blob/02ef2ae540a10466bc7462ac4534fad2d711c627/include/boost/url/url_view.hpp#L1911


---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 20:55:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843035574  

📁 include/boost/url/bnf/parse.hpp

```diff
 247 | 
 272 |-    @param[out] t0 T0 1st BNF element
 248 |+    @throw system_error on failure
```

> Username: vinniefalco  
> Created_at: 2022-01-03 20:55:36 UTC  
> Updated_at: 2022-01-03 20:55:37 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777699815  

What does this look like in the output

> Username: alandefreitas  
> Created_at: 2022-01-03 21:11:42 UTC  
> Updated_at: 2022-01-03 21:11:43 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777706141  

![image](https://user-images.githubusercontent.com/5369819/147980817-6c4633b4-1e26-4e61-b44c-178c325e6b0e.png)

> Username: alandefreitas  
> Created_at: 2022-01-03 21:12:33 UTC  
> Updated_at: 2022-01-03 21:13:12 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777706505  

The only reason I changed it is because of our discussion in https://github.com/CPPAlliance/url/pull/97#discussion_r777646387 though

> Username: vinniefalco  
> Created_at: 2022-01-03 21:29:00 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777713135  

Now it says "Thrown on" ... "on failure" which looks weird. What does ASIO say?

> Username: alandefreitas  
> Created_at: 2022-01-04 00:18:05 UTC  
> Updated_at: 2022-01-04 00:18:06 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777764179  

ASIO says  
  
```  
@throws boost::system::system_error Thrown on failure.  
```  
  
I can use that instead of the stock phrase in https://github.com/CPPAlliance/url/pull/97#discussion_r777646387, right?  
  
I don't really like the passive voice in that case but well... conventions...

> Username: vinniefalco  
> Created_at: 2022-01-04 00:53:38 UTC  
> Updated_at: 2022-01-04 00:53:39 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777771761  

`@throws system_error Thrown on failure.` works. Note that the library provides the type alias `system_error`.

> Username: alandefreitas  
> Created_at: 2022-01-04 01:08:39 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777774978  

> Note that the library provides the type alias system_error.  
  
Sure. It's only the passive voice in "Thrown" I didn't like. But it's OK.


---

## Review 25 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:24:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843050862  

📁 include/boost/url/bnf/parse.hpp

```diff
  49 | 
  32 |- /** @brief Parse a literal character
  50 |+ /** Parse a substring as a BNF literal character
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:24:02 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777711043  

you don't need to say "BNF" here, just "Parse a literal character"

> Username: alandefreitas  
> Created_at: 2022-01-04 00:19:32 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777764444  

OK. I removed it from all functions.


---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:24:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843050960  

📁 include/boost/url/bnf/parse.hpp

```diff
  49 |-    auto const end = it + str.size();
  61 |+    char* it = str.data();
  62 |+    char* const end = it + str.size();
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:24:13 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777711132  

shouldn't these be `char const*`?

> Username: alandefreitas  
> Created_at: 2022-01-04 00:27:59 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777766265  

Oh... Just `char*` would have been fine but `char* const end` won't work because the functions expect `char const* end` instead. I expected it to be `char* const` and assumed the other `const` (for `char`) would be implicit, just to make a point. This assumption I made *might* indicate a problem:  
  
The overloads expect `char const*& it, char const* end`, which might be a problem. If I understand it correctly, `char const` is correct but they should receive `char const * const end` because it's part of the contract that `end` cannot be modified.

> Username: vinniefalco  
> Created_at: 2022-01-04 00:43:38 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777769626  

There is no difference between these two function signatures:  
```  
void f( char const* );  
void f( char const* const );  
```  
  
Whether an argument passed by value is unmodifiable (i.e. marked const) is information only visible inside the function definition. The mangled name never includes the const qualifier of arguments passed by value. Either of these works for the example:  
```  
   char const* it = str.data();  
   char const* end = it + str.size();  
```  
or  
```  
   char const* it = str.data();  
   char const* const end = it + str.size();  
```

> Username: alandefreitas  
> Created_at: 2022-01-04 00:55:57 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777772310  

> There is no difference between these two function signatures:  
  
Oh... Of course.   
  
I forgot for some reason the pointer was still being passed by value. :facepalm:


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:24:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843051122  

📁 include/boost/url/bnf/parse.hpp

```diff
  61 |+    char* it = str.data();
  62 |+    char* const end = it + str.size();
  63 |    if (bnf::parse(it, end, ec, 'H')) {
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:24:32 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777711259  

still wrong... no namespace qualifier, where's the `using`?

> Username: alandefreitas  
> Created_at: 2022-01-04 00:30:10 UTC  
> Updated_at: 2022-01-04 00:30:17 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777766746  

OK. My rationale was the same as in https://github.com/CPPAlliance/url/pull/97#discussion_r777752535, but I get it now.


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:25:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843051501  

📁 include/boost/url/bnf/parse.hpp

```diff
  77 | 
  66 |-    @param[in] end An iterator to one past the last element in the range
  78 |+    @param end An iterator to one past the last element in the range
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:25:13 UTC  
> Updated_at: 2022-01-03 21:25:14 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777711584  

I think you can just say "An iterator to the end of the range." Please verify

> Username: alandefreitas  
> Created_at: 2022-01-04 00:37:04 UTC  
> Updated_at: 2022-01-04 00:37:05 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777768212  

OK. Done.  
  
The reason I used "one past the last element" is the previous javadoc.hpp file I used as reference had explicit instructions to use "one past the last element". I think cppreference also uses "iterator to the element following the last element of the _____" unless when summarizing. Describing `end` in terms of `end`, at best, seemed circular in what matters the most, and at worst, implies `end` refers to the last element to anyone who depends on it.

> Username: vinniefalco  
> Created_at: 2022-01-04 00:50:19 UTC  
> Updated_at: 2022-01-04 00:51:08 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777771041  

I guess either one works. We have to assume that the reader understands C++ ranges and iterators, and that `[begin, end)` is a half-open interval. Otherwise we end up repeating this common knowledge over and over and the docs look clunky.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:25:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843051883  

📁 include/boost/url/bnf/parse.hpp

```diff
  83 |+ 
  84 |+    @see
  85 |+        @ref error.
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:25:52 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777711850  

You don't need to ref these error because it is implied. Otherwise we end up with ref error on almost every function in the library, which is redundant.

> Username: alandefreitas  
> Created_at: 2022-01-04 00:37:51 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777768376  

OK.


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:28:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843053515  

📁 include/boost/url/bnf/parse.hpp

```diff
 103 |+    elements in `args`. For each `arg` in `args`, a  corresponding function overload of `parse`
 104 |+    `bool parse(char const*& it, char const* const end, error_code&, Arg const& arg)`
 105 |+    should be defined to consume the input from [`it`, `end`).
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:28:09 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777712837  

This needs an example for a hypothetical user-defined type `T`.

> Username: alandefreitas  
> Created_at: 2022-01-04 00:39:10 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777768680  

This is what we had before, but my understanding was that you thought it was too long. Any recommendations?

> Username: vinniefalco  
> Created_at: 2022-01-04 00:52:54 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777771581  

Yes we can sit together and work on it.


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-03 21:29:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843054300  

📁 include/boost/url/bnf/parse.hpp

```diff
 276 |-    @param[out] tn ...Tn [3rd, 4th, ...] BNF elements
 247 |+    @par References
 248 |+    @li [Backus–Naur form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)
```

> Username: vinniefalco  
> Created_at: 2022-01-03 21:29:29 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777713346  

The wikipedia entry for Backus-Naur form in no way helps explain what `parse` does so why link it?

> Username: alandefreitas  
> Created_at: 2022-01-04 00:40:28 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777768962  

Because it's what is being parsed. But that's already been removed for another reason anyway.


---

## Comment 32

> Username: vinniefalco  
> Created_at: 2022-01-04 00:39:35 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1004449743  

Can we please squash the whole thing down

---

## Comment 33

> Username: alandefreitas  
> Created_at: 2022-01-04 00:40:40 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1004450149  

> Can we please squash the whole thing down  
  
OK

---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:18:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843138335  

📁 doc/qbk/4.0.BNF_parsing.qbk

```diff
  11 |+ [section:charset BNF Parsing]
  12 |+ 
  13 |+ The file [@../../../../boost/url/bnf/parse.hpp `<boost/url/bnf/parse.hpp>`] contains the basic overloads of the function __bnf__parse__ for parsing BNF elements.
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:18:41 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777260  

No need to mention the file, and refs should use the full link

> Username: alandefreitas  
> Created_at: 2022-01-04 01:30:58 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777780016  

> refs should use the full link  
Why is that? Why are the macros such as   
  
```  
[def __authority_view__         [link url.ref.boost__urls__authority_view `authority_view`]]  
```   
  
defined in 0.main.qbk then?

> Username: alandefreitas  
> Created_at: 2022-01-04 01:31:08 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777780072  

> refs should use the full link  
Why is that? Why are the macros such as   
  
```  
[def __authority_view__         [link url.ref.boost__urls__authority_view `authority_view`]]  
```   
  
defined in 0.main.qbk then?


---

## Review 35 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:18:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843138411  

📁 doc/qbk/4.0.BNF_parsing.qbk

```diff
  18 |+ 
  19 |+ * Is default constructible
  20 |+ * Holds the result, usually as a reference, of parsing
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:18:57 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777305  

We don't use references anymore because those can't be default-constructed


---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:19:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843138522  

📁 doc/qbk/4.0.BNF_parsing.qbk

```diff
  20 |+ * Holds the result, usually as a reference, of parsing
  21 |+ * Has a nested value_type
  22 |+ * Has `value_type& operator*() const noexcept`
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:19:18 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777399  

The nested type and `operator*` is only needed to work with `list` and `range`, this is a work in progress.


---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2022-01-04 01:19:23 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1004462359  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 38 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:19:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843138600  

📁 doc/qbk/4.0.BNF_parsing.qbk

```diff
  30 |+ on [@https://en.cppreference.com/w/cpp/language/adl Argument Dependent Lookup (ADL)] to find these overloads.
  31 |+ 
  32 |+ [endsect]
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:19:32 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777445  

missing newline


---

## Review 39 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:20:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843138772  

📁 include/boost/url/bnf/parse.hpp

```diff
  42 |-    the function returns `false` and sets the error number in @ref ec to the
  43 |-    appropriate error number defined in @ref boost::urls::error.
  29 |+    The initial character in the range is immediately consumed and ignored.
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:20:06 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777566  

This is a little confusing because it implies that the character is consumed no matter what, which is incorrect.


---

## Review 40 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:20:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843138892  

📁 include/boost/url/bnf/parse.hpp

```diff
  56 | 
  68 |-    @param[out] ec The error code this function should set @ref if it fails
  57 |+    @param ch A literal character
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:20:34 UTC  
> Updated_at: 2022-01-04 01:20:35 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777649  

You could say "the character to match"


---

## Review 41 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:21:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843139063  

📁 include/boost/url/bnf/parse.hpp

```diff
 168 |-      return 0;
  78 |+    using bnf::parse;
  79 |+    if (parse(it, end, ec, bnf_element_1, bnf_element_2, bnf_element_3)) {
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:21:14 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777757  

ugh bnf_element_1... too long! This makes it hard to read and distinguish variables from types. How about `t1`, `t2`, and `t3`?

> Username: vinniefalco  
> Created_at: 2022-01-04 01:21:22 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777770  

we need to work on this

> Username: alandefreitas  
> Created_at: 2022-01-04 01:27:07 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777779060  

> ugh bnf_element_1... too long! This makes it hard to read and distinguish variables from types  
  
Maybe `bnf1`, `bnf2`, and `bnf3`?  
  
> ugh bnf_element_1... too long! This makes it hard to read and distinguish variables from types. How about t1, t2, and t3?  
  
From the example only, how is the user going to know [t1, t2, t3] are bnf elements?


---

## Review 42 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 01:21:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-843139224  

📁 include/boost/url/bnf/parse.hpp

```diff
 215 |-    elements [@ref t0, @ref t1, ...]. If the string is not completely consumed,
 216 |-    @ref ec is set to @ref error::leftover_input.
 126 |+    This function parses a complete string `s` into the Backus–Naur form (BNF)
```

> Username: vinniefalco  
> Created_at: 2022-01-04 01:21:49 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r777777906  

just bnf elements


---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2022-01-04 01:39:24 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1004468011  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2022-01-04 22:19:27 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1005213170  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 45 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 22:59:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-844081426  

📁 include/boost/url/grammar/parse.hpp

```diff
 135 |+     R0&& r1,
 136 |+     R1&& r2,
 137 |+     Rs&&... rs
```

> Username: vinniefalco  
> Created_at: 2022-01-04 22:59:01 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778442895  

I use `Rn&& rn` but `Rs&& rs` works too I guess

> Username: alandefreitas  
> Created_at: 2022-01-04 23:08:34 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778446728  

I thought about `Rn` too, the problem is `Rn` usually indicates the last element instead of a sequence of elements. Of course, that makes little difference to anyone who understands variadic templates but still...


---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2022-01-04 23:19:26 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1005243772  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 47 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 23:38:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-844099248  

📁 include/boost/url/grammar/parse.hpp

```diff
  92 |+     is required to call this function appropriately with for
  93 |+     argument-dependent lookup for other grammar rules, and
  94 |+     calls to `parse` should be unqualified.
```

> Username: vinniefalco  
> Created_at: 2022-01-04 23:37:58 UTC  
> Updated_at: 2022-01-04 23:38:03 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778457142  

"As other overloads of..." doesn't sound right. How about:  
  
> The statement `using grammar::parse` should be in scope before any calls to `parse` are made without namespace qualification, so that argument-dependent lookup can take effect, as seen in this example:

> Username: alandefreitas  
> Created_at: 2022-01-04 23:40:40 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778458136  

Better


---

## Review 48 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 23:38:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-844100420  

📁 include/boost/url/grammar/parse.hpp

```diff
  65 | 
  80 |- /** @brief Parse a sequence of elements
  66 |+ /** Parse two or more sequential grammar rules
```

> Username: vinniefalco  
> Created_at: 2022-01-04 23:38:50 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778457483  

How about just "Parse a range of characters"

> Username: alandefreitas  
> Created_at: 2022-01-04 23:43:22 UTC  
> Updated_at: 2022-01-04 23:43:23 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778459028  

OK. I like all of them:  
  
- "substring" or "range of characters" end up being synonymous here.   
- "string" could imply "substring" in this context  
-  even "" seems ok as parsing a grammar rule already implies there's a string being parsed


---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2022-01-04 23:49:27 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1005259027  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Review 50 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-04 23:52:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/97#pullrequestreview-844106051  

📁 doc/qbk/4.0.grammars.qbk

```diff
  16 |+ according to grammar rules.
  17 |+ 
  18 |+ A grammar rule object implements the rules for parsing a grammar rule and stores parsing results.
```

> Username: vinniefalco  
> Created_at: 2022-01-04 23:52:42 UTC  
> Updated_at: 2022-01-04 23:52:48 UTC  
> Url: https://github.com/boostorg/url/pull/97#discussion_r778462008  

Consider  
  
> A grammar rule type (henceforth called "rule") implements...


---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2022-01-04 23:59:26 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1005263045  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 52

> Username: cppalliance-bot  
> Created_at: 2022-01-05 00:24:26 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1005275117  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 53

> Username: cppalliance-bot  
> Created_at: 2022-01-05 00:29:31 UTC  
> Url: https://github.com/boostorg/url/pull/97#issuecomment-1005277462  

An automated preview of the documentation is available at [https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://97.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
