# #670 Symmetrizing read/write wkt [Merged]

> Username: sudo-panda  
> Created at: 2020-02-29 12:51:58 UTC  
> Updated at: 2021-03-03 13:06:08 UTC  
> Merged at: 2021-03-03 13:06:08 UTC  
> Closed at: 2021-03-03 13:06:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/670  

This PR fixes #654 . It adds the following:  
  
- `to_wkt`: This function converts the given geometry to wkt string and returns it instead of modifying a stringstream taken as input like `wkt`.  
- `from_wkt`: This function returns the geometry obtained by parsing a wkt string instead of taking in a geometry variable as parameter and storing the geometry in it.  
- tests: Tests for both `to_wkt` and `from_wkt` are provided with basically follow the template of already implemented `read_wkt` and `wkt` tests.  
- examples and documentation: Example usage of both have been added and documentation has been updated.  
  
This PR is now ready to be reviewed and then merged.

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-02-29 23:43:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-366825743  

Although needs some changes, it's looking good.  
  
Please, consider also  
  
- new example in `oc/src/examples/io/`  
- finding if there is place for documentation (look for `.qbk` files for other `wkt` functions)

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  67 |+ {
  68 |+     template <typename T>
  69 |+     static void add(Output & out, T const& v)
```

> Username: mloskot  
> Created_at: 2020-02-29 23:33:31 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386063399  

Perhaps [put](https://en.cppreference.com/w/cpp/io/basic_ostream/put) is a better name, than `add`.  
Alternatively, `append` if it should work for both, streams and buffers like `string`. What's your opinion, @awulkiew ?  
  
Nitpick: remove whitespaces before `&`. Try to replicate existing code style.

> Username: awulkiew  
> Created_at: 2020-03-01 14:48:40 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386114027  

@mloskot yes, `append` is a better name, thanks.

---

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  89 |+     }
  90 |+ 
  91 |+     static void add(std::string & out, const char * s)
```

> Username: mloskot  
> Created_at: 2020-02-29 23:34:13 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386063437  

Nitpick: `const char *` -> `char const*`

> Username: awulkiew  
> Created_at: 2020-03-01 14:54:01 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386114435  

Does this rule also apply for pointers, or only references? I have a feeling that this can be confused with `char * const` by a beginner.

> Username: awulkiew  
> Created_at: 2020-03-01 14:59:18 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386114818  

Also of you change `std::string` to `std::basic_string` then it becomes `CharT const*` or `const CharT *`.

> Username: mloskot  
> Created_at: 2020-03-01 17:56:36 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386127344  

```  
T const&  
T const*  
```  
  
is clearest yet most symmetric syntax possible, also for pointers as even if one writes `const T*`, correct reading is from right to left anyway. I'd actually expect beginners having problems with `const T*` ;)  
  
This was discussed quite some time ago and documented here https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers

---

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  77 |+ {
  78 |+     template <typename T>
  79 |+     static void add(std::string & out, T const& val)
```

> Username: mloskot  
> Created_at: 2020-02-29 23:37:34 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386063594  

`std::string` needs to be `std::basic_string`

> Username: awulkiew  
> Created_at: 2020-03-01 14:58:23 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386114738  

And then also take `std::basic_stream` as argument of `to_wkt`.

---

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  79 |+     static void add(std::string & out, T const& val)
  80 |+     {
  81 |+         std::stringstream ss;
```

> Username: mloskot  
> Created_at: 2020-02-29 23:38:46 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386063654  

`std::stringstream` needs to be `std::basic_stringstream`


📁 test/io/wkt/wkt.cpp

```diff
  47 | }
  48 | 
  49 |+ template <typename G>
```

> Username: mloskot  
> Created_at: 2020-02-29 23:40:00 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386063756  

I think you should also update copyright notice, adding yourself.

> Username: sudo-panda  
> Created_at: 2020-03-01 05:32:08 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386078689  

What year should I mention in the copyright notice?

> Username: mloskot  
> Created_at: 2020-03-01 09:24:48 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386091610  

Year of your contribution.  
  
See also FAQ on maintaining copy notices here https://www.boost.org/users/license.html


---

## Review 2 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-02 22:56:04 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-367582737  

This is getting complete. I just pointed out a bunch of issues.

📁 doc/html/index.html

```diff
 140 | <li class="listitem">
```

> Username: mloskot  
> Created_at: 2020-03-02 22:40:01 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386694785  

Please, remove changes to this file.   
Unfortunately, it gets modified during docs build, see #674, but the changes should be `git restore`-ed before committing.


📁 doc/src/examples/io/from_wkt.cpp

```diff
  28 |+     polygon_type c = boost::geometry::from_wkt<polygon_type>("POLYGON((0 0,0 7,4 2,2 0,0 0))");
  29 |+     box_type d = boost::geometry::from_wkt<box_type>("BOX(0 0,3 3)");
  30 |+     segment_type e = boost::geometry::from_wkt<segment_type>("SEGMENT(1 0,3 4)");
```

> Username: mloskot  
> Created_at: 2020-03-02 22:46:13 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386697442  

Could you declare the variables first, then assign to avoid the `warning: variable ‘a’ set but not used` flood?  
  
Generally, we don't want to introduce any new warnings. They also flood CI builds logs often causing logs too log  failures or timeouts, and we may start building examples at some point.


📁 doc/reference/io/read_wkt.qbk

```diff
  21 | [heading See also]
  22 |+ * [link geometry.reference.io.wkt.from_wkt From WKT]
  23 |+ * [link geometry.reference.io.wkt.to_wkt To WKT]
```

> Username: mloskot  
> Created_at: 2020-03-02 22:55:13 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386701087  

You need to create the two documents you are linking here, which will pull the examples in:  
  
1. `reference/io/from_wkt.qbk`  
  
    ```  
    [heading Conformance]  
    Other libraries refer to this functionality as [*ST_GeomFromText] or [*STGeomFromText].  
    That is not done here because Boost.Geometry support more text formats. The name GeomFromText  
    is reserved for future usage, which will then have an indication of the used text format.  
  
    [heading Example]  
    [from_wkt]  
  
    [heading See also]  
    * [link geometry.reference.io.wkt.to_wkt To WKT]  
    * [link geometry.reference.io.wkt.wkt WKT streaming manipulator]  
    ```  
  
2. `reference/io/to_wkt.qbk`  
  
    ```  
    [heading Conformance]  
    Other libraries refer to this functionality as [*ST_AsText].  
    That is not done here because Boost.Geometry support more text formats.  
  
    [heading Example]  
    [to_wkt]  
  
    [heading See also]  
    * [link geometry.reference.io.wkt.from_wkt From WKT]  
    * [link geometry.reference.io.wkt.wkt WKT streaming manipulator]  
    ```

> Username: sudo-panda  
> Created_at: 2020-03-03 10:33:09 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r386929712  

I made those files but did not commit them. Sorry about that.


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2020-03-03 13:55:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-367996402  

Looks OK to me. Thanks!

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-03 17:44:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368189761  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  75 |+ 
  76 |+ template <typename CharT, typename Traits, typename Allocator>
  77 |+ struct output_formatter<std::basic_string<CharT, Traits, Allocator>>
```

> Username: awulkiew  
> Created_at: 2020-03-03 17:44:16 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387186603  

`> >` - we don't require C++11 yet.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-03 17:45:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368190365  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  90 |+     }
  91 |+ 
  92 |+     static void append(std::basic_string<CharT, Traits, Allocator>& out, const CharT* s)
```

> Username: awulkiew  
> Created_at: 2020-03-03 17:45:11 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387187078  

As @mloskot said earlier the type should be `CharT const*`

> Username: awulkiew  
> Created_at: 2020-03-03 18:06:23 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387198814  

The problem with this overload and the one above is that they take `CharT` and `CharT const*` which are taken from `std::basic_string`. So if this type is anything else then the first overload of `append()` taking any `T const&` is going to be instantiated. This will happen e.g. when you pass `std::wstring` because this type is derived from `std::basic_string<wchar_t>` [1] so `CharT` will be `wchar_t` but below values of type `char` or `char const*` are passed into `append()`, e.g. `' '` or `"("`.  
  
This is not a problem now, because `to_wkt` always returns `std::string` but if you wanted it to be complete, and e.g. allowed to define the output string type, e.g. like that:  
  
```  
std::wstring wkt = bg::to_wkt<std::wstring>(geom);  
```  
  
then this implementation would be sub-optimal because for all chars and string literals `std::basic_stringstream` would be used anyway.  
  
[1] https://en.cppreference.com/w/cpp/string/basic_string

> Username: mloskot  
> Created_at: 2020-03-03 20:49:19 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387284769  

I agree with @awulkiew here. I missed the fact all the current WKT IO implementation, by design or not, quite assumes `char`-based output.  
  
Adding support for arbitrary `CharT` is something that could be considered but in separate PR.  
  
Then, with help of [additional layer of indirection](http://www.josuttis.com/tmplbook/), we could specify required characters and WKT tags too using expected types, somethign along these lines:  
  
```cpp  
template <typename CharT>  
struct output_char_traits;  
template <>  
struct output_char_traits<char> { static const char space = ' '; };  
template <>  
struct output_char_traits<wchar_t> { static const wchar_t space = L' '; };  
  
template <typename Output>  
void apply(Output& out)  
{  
    // traits_type common for basic_string and basic_stringstream  
    typedef typename Output::traits_type::char_type char_type;  
    output_formatter<Output>::append(out, output_char_traits<char_type>::space);  
};  
```

> Username: awulkiew  
> Created_at: 2020-03-03 22:34:37 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387335810  

It is also possible that this could be simplified. I didn't look into it but it's possible that the codes of the basic characters we use (' ', '(', ')', ',', etc.) are the same for all character types (`char`, `wchar_t`, `char16_t`, `char32_t`, etc.). So it's possible that a simple `static_cast` would be enough.

> Username: mloskot  
> Created_at: 2020-03-03 22:50:13 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387342476  

The cast may not work in a endian-agnostic manner. There is however narrow/widen functions defined and the standard guarantees unambiguous transformation for the [basic charactes  set of 96 characters](https://books.google.pl/books?id=5qybChHSk7QC&pg=PA279&lpg=PA279&dq=char+widen+narrow&source=bl&ots=drhQ3F5n20&sig=ACfU3U0bF4Ro_vwSXVE56F9TGIytpMrWPQ&hl=en&sa=X&ved=2ahUKEwjWiajLsP_nAhXksIsKHd3_DocQ6AEwA3oECAYQAQ#v=onepage&q=char%20widen%20narrow&f=false) which includes space.

> Username: sudo-panda  
> Created_at: 2020-03-04 00:39:39 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387378927  

As you have said that the support for CharT should be added in a separate PR, what is to be done in this PR?

> Username: mloskot  
> Created_at: 2020-03-04 08:14:28 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387505454  

@sudo-panda As you see the review turned into some additional brainstorm, not uncommon.   
  
I think reverting back to explicit use of narrow character `std::string` and `std::stringstream` is probably the right thing to do, but let's wait for @awulkiew opinion on that.  
  
Extending current all the plain text formatters (WKT and even SVG) to support arbitrary type of character is something desirable, I think, but it is a task separate from this PR.

> Username: awulkiew  
> Created_at: 2020-03-05 13:14:02 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388284119  

> I think reverting back to explicit use of narrow character std::string and std::stringstream is probably the right thing to do  
  
@mloskot Yes, I'm ok with that.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-03 17:47:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368191773  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  80 |+     static void append(std::basic_string<CharT, Traits, Allocator>& out, T const& val)
  81 |+     {
  82 |+         std::basic_stringstream<CharT, Traits, Allocator> ss;
```

> Username: awulkiew  
> Created_at: 2020-03-03 17:47:13 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387188244  

Prior to C++11 `basic_stringstream` didn't have `Allocator` parameter [1] so please remove it. Plus it's probably not needed to use a custom allocator here.  
  
[1] https://en.cppreference.com/w/cpp/io/basic_stringstream


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-03 17:52:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368195484  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  99 |+         std::basic_string<CharT, Traits, Allocator>& out, 
 100 |+         std::basic_string<AppendCharT, AppendTraits, AppendAllocator> const& str
 101 |+         )
```

> Username: awulkiew  
> Created_at: 2020-03-03 17:52:26 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387191195  

I know I had this overload in my example code but is this really needed?  
Are strings passed into `append()` anywhere?


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-03 18:09:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368207223  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  81 |+     {
  82 |+         std::basic_stringstream<CharT, Traits, Allocator> ss;
  83 |+         ss << val;
```

> Username: awulkiew  
> Created_at: 2020-03-03 18:09:41 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387200492  

As I mentioned here: https://github.com/boostorg/geometry/issues/654#issuecomment-570940504 I propose to set the precision here to `std::numeric_limits<T>::digits10` if `std::numeric_limits<T>::is_specialized`. @mloskot what do you think?

> Username: mloskot  
> Created_at: 2020-03-03 20:54:08 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387287210  

I agree maxing out by `T` should be fine.  
  
We could have something like this to address user requests like https://github.com/boostorg/geometry/issues/654#issuecomment-570835846  
  
```cpp  
std::string to_wkt(Geometry const& geometry); // defaults to digits10  
std::string to_wkt(Geometry const& geometry, int significant_digits);

> Username: sudo-panda  
> Created_at: 2020-03-06 05:56:00 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388725112  

Can you provide any technique to propagate the significant_digits back to the output_formatter?  
The ways that I see its possible are   
- Use a tparam or param in each function call from to_wkt till output_formatter  
- Use a global variable  
  
  
Both of these seem to be bad. The latter worse than the former. If you could provide any other way or else I will proceed with the first one.

> Username: mloskot  
> Created_at: 2020-03-06 09:12:39 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388787946  

@sudo-panda   
> Use a tparam or param in each function call from to_wkt till output_formatter  
  
IMO, users typically expect it to be specifiable  at run-time, so yes, adding the parameter is the feasible option `output_formatter<T>::append(..., int significant_digits)` .  
  
> Use a global variable  
  
No, we definitely should avoid globals.  
  
> Both of these seem to be bad.  
  
What's bad in an extra parameter?  
  
/cc @vissarion, @awulkiew for insights

> Username: sudo-panda  
> Created_at: 2020-03-06 09:16:48 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388789866  

> What's bad in an extra parameter?  
  
It's redundant information for the functions that are between to_wkt and output_formatter. But if that's the only way then I will do it. I thought maybe there was another way that I am ignorant of.

> Username: mloskot  
> Created_at: 2020-03-06 09:28:16 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388795478  

The existing technique of dispatching, often interleaved (for geometry type, for output type), is based on forwarding of (numerous) parameters which are often ignored at higher levels until they reach place where they find its use. Think of it as functional way.  
  
Alternative would be object-based solution, for example:  
  
```cpp  
template <typename Geometry>  
inline std::string to_wkt(Geometry const& geometry, int signigicant_digits)  
{  
    concepts::check<Geometry const>();  
    std::string out;  
    wkt<dispatch::devarianted_wkt<Geometry>> wkt(out, signigicant_digits);  
    wkt.write(out, geometry);  
    return out;  
}  
```  
  
but then `wkt` and others would have to undergo a major re-write.  
  
To me, this is a matter of work-to-feature balance. Adding support for how many decimal digits we output may not necessarily justify a major change to existing code. Major changes have real potential to introduce bugs. Is the feature worth the risk...

> Username: sudo-panda  
> Created_at: 2020-03-06 09:36:02 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388799579  

You are right it is not worth the risk and is not how rest of boost/geometry has been built. I will start work on adding the parameter.

> Username: mloskot  
> Created_at: 2020-03-06 09:42:20 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388802719  

If you are not in rush, you may give @awulkiew  and @vissarion some time to respond with their opinions.

> Username: sudo-panda  
> Created_at: 2020-03-06 09:43:16 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388803183  

Sure

> Username: vissarion  
> Created_at: 2020-03-06 11:31:14 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388853474  

To my point of view, passing an extra parameter seems ok and as @mloskot said it is already performed in the library in many places.

> Username: awulkiew  
> Created_at: 2020-03-17 16:24:16 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393805981  

I'm also ok with an extra parameter.  
  
The only problem I see is that a user may want to pass a set of such parameters, i.e. stream manipulators for coordinates. But this problem is solvable by e.g. allowing to optionally pass a `std::pair` or `std::tuple` of manipulators in the future:  
  
    bg::to_wkt(geometry);  
    bg::to_wkt(geometry, int_precision_10);  
    bg::to_wkt(geometry, std::make_pair(std::fixed, std::setprecision(10)));  
  
unless you have a better idea of an extensible interface. Just have in mind that in the future we may have to allow passing a strategy into this function as with any other function.

> Username: mloskot  
> Created_at: 2020-03-17 16:55:06 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393827428  

Why not just let user give own pre-configured stream?  
```  
to_wkt(std::ostream& os, geometry);  
```


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-03 18:17:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368212562  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 113 |+         if (I > 0)
 114 |+         {
 115 |+             output_formatter<Output>::append(out, ' ');
```

> Username: awulkiew  
> Created_at: 2020-03-03 18:17:50 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387204697  

So e.g. here `' '` is of type `char` so if the `Output` is `std::basic_string<wchar_t>` then here the `append()` using `std::basic_stringstream` will be instantiated defeating the purpose of having multiple `append()` overloads.

> Username: mloskot  
> Created_at: 2020-03-03 20:54:57 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387287617  

Yes, as I also agreed in details in  https://github.com/boostorg/geometry/pull/670#discussion_r387284769


---

## Review 10 [Commented]

> Username: vissarion  
> Created_at: 2020-03-04 11:24:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-368698531  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  75 |+ 
  76 |+ template <typename CharT, typename Traits>
  77 |+ struct output_formatter<std::basic_string<CharT, Traits>>
```

> Username: vissarion  
> Created_at: 2020-03-04 11:23:25 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387604583  

`> >` - we don't require `C++11` yet.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-04 22:17:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-369165402  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  75 |+ 
  76 |+ template <typename CharT, typename Traits>
  77 |+ struct output_formatter<std::basic_string<CharT, Traits>>
```

> Username: awulkiew  
> Created_at: 2020-03-04 22:17:28 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r387968294  

`std::basic_string` has 3 template arguments prior to C++11. `std::basic_string` takes `Allocator` [1] while `std::basic_stringstream` does not [2].  
  
[1] https://en.cppreference.com/w/cpp/string/basic_string  
[2] https://en.cppreference.com/w/cpp/io/basic_stringstream

> Username: sudo-panda  
> Created_at: 2020-03-05 02:31:17 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r388047881  

Thankyou for the explanation.


---

## Comment 12

> Username: mloskot  
> Created_at: 2020-03-05 11:06:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-595171381  

@sudo-panda FYI, next time you update your PR, then documentation and `doc/src/examples` will be built and verified with GitHub Actions.

---

## Review 13 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-07 21:04:57 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-370788755  

@sudo-panda Thanks for another round of your efforts. This to me is looking good, just having two minor requests.

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 112 |+         if(std::numeric_limits<T>::is_specialized)
 113 |+         {
 114 |+             if(significant_digits==-1)
```

> Username: mloskot  
> Created_at: 2020-03-07 21:01:00 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r389311850  

IMO, exact value is not relevant here and the test should be   
  
```  
if (significant_digits < 0)  
```  
  
IOW, we want to distinguish specified precision (0, 1, 2, 3...) from unspecified (default).

---

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 109 |+     template <typename T>
 110 |+     static void append(std::ostream& out, T const& val, int significant_digits = -1)
 111 |+     {
```

> Username: mloskot  
> Created_at: 2020-03-07 21:04:16 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r389312046  

I think it would be a good idea to add here  
  
```  
BOOST_STATIC_ASSERT((boost::is_arithmetic<T>::value));  
```  
  
It would be nice if we could use `static_assert` with `std::is_arithmetic<T>` here , but we are not at C++11 yet.


---

## Comment 14

> Username: mloskot  
> Created_at: 2020-03-09 23:42:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-596830777  

@sudo-panda If you update your PR, you will get #680 change and GitHub Actions will CI check it for you.

---

## Review 15 [Approved]

> Username: mloskot  
> Created_at: 2020-03-14 21:52:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-374756499  

This looks good to me and ready to merge.  
If no objections arrive from @awulkiew  or @vissarion in next days, I'll merge it.  
  
I assume @barendgehrels generally agrees on such utility functions as per his https://github.com/boostorg/geometry/issues/654#issuecomment-570597215 and https://github.com/boostorg/geometry/issues/654#issuecomment-570926793  
  
@sudo-panda Thanks for your work!

---

## Comment 16

> Username: sudo-panda  
> Created_at: 2020-03-14 22:25:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-599142500  

@mloskot thanks for the guidance and approving the PR.

---

## Review 17 [Commented]

> Username: vissarion  
> Created_at: 2020-03-17 13:16:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376021227  

Thanks I am ok with merging.

📁 test/io/wkt/wkt.cpp

```diff
 116 |+ 
 117 |+     /*
 118 |+     std::cout << "n=" << bg::num_points(geometry)
```

> Username: vissarion  
> Created_at: 2020-03-17 13:15:46 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393669605  

you could use  `#ifdef BOOST_GEOMETRY_TEST_DEBUG` here instead of commenting out

> Username: sudo-panda  
> Created_at: 2020-03-17 14:09:42 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393706378  

@vissarion I will do that, thanks for the suggestions.


---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-17 16:48:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376218494  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
  88 |+                 ss.precision(std::numeric_limits<T>::digits10);
  89 |+             else
  90 |+                 ss.precision(significant_digits);
```

> Username: awulkiew  
> Created_at: 2020-03-17 16:48:14 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393822580  

The precision is set only if `numeric_limits` are specialized for type `T`.

> Username: mloskot  
> Created_at: 2020-03-17 16:52:56 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393825941  

@awulkiew Do you mean this?  
  
```cpp  
if(significant_digits < 0 && std::numeric_limits<T>::digits10 > 0) // or std::numeric_limits<T>::is_specialized as equivalent  
    ss.precision(std::numeric_limits<T>::digits10);  
```

> Username: awulkiew  
> Created_at: 2020-03-17 20:17:17 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393944012  

@mloskot I mean that if a user passes `significant_digits` it means he/she wants it to be set in all cases, i.e.:  
  
    if (significant_digits >= 0)  
        ss.precision(significant_digits);  
    else if (std::numeric_limits<T>::is_specialized)  
        ss.precision(std::numeric_limits<T>::digits10);  
          
or am I missing something?

> Username: mloskot  
> Created_at: 2020-03-17 20:20:32 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393945618  

>  wants it to be set in all cases  
  
that is, regardless if `std::numeric_limits<T>` is specialized or not.  
  
Yes, correct.


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-17 16:53:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376222886  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 112 |+     {
 113 |+         BOOST_STATIC_ASSERT((boost::is_arithmetic<T>::value));
 114 |+         if(std::numeric_limits<T>::is_specialized)
```

> Username: awulkiew  
> Created_at: 2020-03-17 16:53:04 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393826034  

Setting precision is only needed for string output isn't it? Why is it here? Did I miss something?

> Username: mloskot  
> Created_at: 2020-03-17 17:42:15 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393857708  

No, you didn't miss anything.  
  
It's just that both, `wkt` stream manipulator and `to_wkt` function use the same formatter implementation, we just got carried away and applied the dedicated argument a bit too far indeed.

> Username: awulkiew  
> Created_at: 2020-03-17 21:03:02 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393968862  

Ok, so `significant_digits` is used here, and in specialization for `std::string`, but not in the default/unspecialized `output_formatter<Output>` (I'm not saying that it should be, I think it should only be used for `std::string`).  
  
AFAIU this specialization for `std::ostream` was added exactly to support `significant_digits` for streams but it will not work for all of them, only for `std::basic_ostream<char>` as long as `std::ostream` is a typedef of `std::basic_ostream<char>` not a separate class because then the default/unspecialized `output_formatter<Output>` would be used.  
  
Is it guaranteed by the standard that `std::ostream` is a typedef and not a separate class?  
  
So anyway, assuming it is, this will be done only for streams using `char`.  
  
I'd just remove the whole specialization together with `significant_digits` from `bg::wkt` stream manipulator. Is my reasoning ok?

> Username: mloskot  
> Created_at: 2020-03-17 22:23:15 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394004706  

> I'd just remove the whole specialization together with significant_digits from bg::wkt stream manipulator. Is my reasoning ok?  
  
@awulkiew Yes, as I explained in https://github.com/boostorg/geometry/pull/670#discussion_r394002323, you've made good catch of this unnecessary change in the `wkt` manipulator.  
  
@sudo-panda I'm sorry I had not caught that earlier myself. I somehow concentrated on the `to_wkt` addition and overlooked how it affects the existing manipulator.

> Username: sudo-panda  
> Created_at: 2020-03-17 23:56:25 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394036625  

@mloskot no need to say sorry. I guess sometimes you need someone not involved in the process to look at the bigger picture. I am here to learn no matter how long it takes.   
@awulkiew  thanks for the review. I will push all these changes soon.


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-17 16:53:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376223567  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 106 |+ 
 107 |+ template <>
 108 |+ struct output_formatter<std::ostream>
```

> Username: awulkiew  
> Created_at: 2020-03-17 16:53:48 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393826546  

Why is this specialization needed?  
  
I mean streams are already supported by the default/unspecialized `output_formatter<Output>`.


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-17 16:57:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376226519  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 629 | template <typename Geometry>
 547 |- inline wkt_manipulator<Geometry> wkt(Geometry const& geometry)
 630 |+ inline wkt_manipulator<Geometry> wkt(Geometry const& geometry, int significant_digits = -1)
```

> Username: awulkiew  
> Created_at: 2020-03-17 16:57:13 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393828844  

This is not needed here, a user can set precision the old way, using `std::setprecision` for streams.  
Am I missing some discussion about it?

> Username: mloskot  
> Created_at: 2020-03-17 17:10:05 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393837324  

No, I don't think you are. I think it slipped through my review unnoticed

> Username: sudo-panda  
> Created_at: 2020-03-17 17:40:39 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393856695  

In my opinion if we are providing the option to users to set significant digits in `to_wkt` it would seem symmetric to let users set it in `wkt` too. What do you think @awulkiew and @mloskot?

> Username: mloskot  
> Created_at: 2020-03-17 17:49:09 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393862001  

@sudo-panda Yes, it implicitly seemed logical and that's why it had not been caught..  
  
I don't think it is a major flaw in design as for some  this may be more convenient to stream's `precision` call:  
  
```cpp  
std::cout << wkt(g, n)  
```  
On the other hand, that interface is not as capable as the stream's interface, it does not allow other manipulators.   
  
Moreover, we want to avoid confusing users allowing them to do  
  
```cpp  
std::cout.precision(n1);  
std::cout << wkt(g, n2)  
```  
  
That would raise unnecessary questions, wouldn't it?  
  
AFAIU @awulkiew, we don't want to compete with `std::ostream` features.

> Username: awulkiew  
> Created_at: 2020-03-17 20:34:10 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393952727  

> AFAIU @awulkiew, we don't want to compete with std::ostream features.  
  
@mloskot I think so. It doesn't make sense for me to allow passing the precision here. `bg::wkt` is an IO manipulator similar to `std::setprecision` itself. It has a clear job to do. It is not needed to semantically "merge" both manipulators together especially when we consider that `std::setprecision` is only one of the many that the user can use to tweak the output to his/her needs. See: https://en.cppreference.com/w/cpp/io/manip  
  
IMO the correct usage is:  
  
    std::cout << std::setprecision(n1) << wkt(g);  
    // or  
    std::cout << std::setprecision(n1) << std::fixed << std::nouppercase << wkt(g);  
  
I rather see the additional parameter of `bg::to_wkt()` as a "convenient workaround", not as an example of "good design" that should be replicated in other places. To the extent that I'd prefer to remove it, only support the default and force the user to use `bg::wkt` if something else was needed. For a long time we didn't even have `to_wkt` because implementing it with `bg::wkt` is trivial if you really need something like that. But I'm ok with this additional parameter for users convenience if you think this would be useful.

> Username: mloskot  
> Created_at: 2020-03-17 20:51:43 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393962539  

@awulkiew   
>I rather see the additional parameter of bg::to_wkt() as a "convenient workaround", not as an example of "good design" that should be replicated in other places.  
  
The `to_wkt` is just a simpler function offered for convenience and it has limited capabilities in comparison to the streams. It could be more limited as using default precision or it could be less limited as accepting the extra argument, but nothing else, no other equivalents to the manipulators. I wouldn't call it a bad design, rather a choice, a tradeoff.  
  
> I'm ok with this additional parameter for users convenience if you think this would be useful.  
  
Although the low-level plumbing requires some adjustments to allow the porcelain interface,  
I don't think it is a huge interference or it will impose maintenance hassle in future (or does it?). And some users prefer simplified variants of interfaces https://github.com/boostorg/geometry/issues/654#issuecomment-570955873  
  
That's it, there is no deep thoughts behind any of that.

> Username: awulkiew  
> Created_at: 2020-03-17 21:52:05 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393991894  

> I wouldn't call it a bad design, rather a choice, a tradeoff.  
  
Me neither. I don't think it's good. It doesn't mean that it's bad ;). It's just straightforward, ad-hoc solution, not very elegant. We have several functions like that so I'm ok with it. I'm talking about `to_wkt` here. I still think that `wkt` should not have this parameter.  
  
> I don't think it is a huge interference or it will impose maintenance hassle in future (or does it?)  
  
No, I don't think so.

> Username: mloskot  
> Created_at: 2020-03-17 22:17:16 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394002323  

> I still think that wkt should not have this parameter.  
  
Yes, I agree. I think it's a good catch you've made. I admit, it's my bad I missed that is unnecessary indeed.


---

## Review 22 [Commented]

> Username: awulkiew  
> Created_at: 2020-03-17 16:59:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376228815  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 645 |+ */
 646 |+ template <typename Geometry>
 647 |+ inline std::string to_wkt(Geometry const& geometry, int significant_digits = -1)
```

> Username: awulkiew  
> Created_at: 2020-03-17 16:59:49 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393830615  

I don't like this design with a magic number. Shouldn't there be 2 separate overloads of `to_wkt`?  
@mloskot @vissarion what is your opinion?

> Username: mloskot  
> Created_at: 2020-03-17 17:02:48 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393832635  

The magic of the number is to encode boolean indicator and actual value with single parameter, to distinguish `<0` for not set (use default) or `>=0` for set (use given).  
  
I don't mind separate overloads. So, @awulkiew whatever is your preferred suggestion

> Username: sudo-panda  
> Created_at: 2020-03-17 17:35:58 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393853745  

What is the final call then? Should I implement seperate overloads?

> Username: awulkiew  
> Created_at: 2020-03-17 21:06:51 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r393970904  

I'm ok with leaving it as it is or removing the parameter at all forcing the user to use `bg::wkt` if he/she needs something different than the default.  
  
@barendgehrels do you have an opinion about it?

> Username: vissarion  
> Created_at: 2020-03-18 10:31:53 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394246628  

Although, I do not have strong opinion, here is my suggestion in order of preference: different overloads, leaving as is, totally removing the parameter.


---

## Review 23 [Commented]

> Username: vissarion  
> Created_at: 2020-03-18 10:29:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376735290  

📁 include/boost/geometry/io/wkt/write.hpp

```diff
 114 |+         if(std::numeric_limits<T>::is_specialized)
 115 |+         {
 116 |+             if(significant_digits < 0)
```

> Username: vissarion  
> Created_at: 2020-03-18 10:28:09 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394244506  

a space after `if` is needed, please also use brackets for `if...else`

> Username: vissarion  
> Created_at: 2020-03-18 10:28:58 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394244935  

there are other cases of missing spaces after `if`, so please address them as well


---

## Comment 24

> Username: sudo-panda  
> Created_at: 2020-03-18 14:41:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-600662058  

I had to force push because of wrong commit message. Will be more careful in the future.

---

## Review 25 [Changes requested]

> Username: barendgehrels  
> Created_at: 2020-03-18 22:53:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-376888519  

As mentioned in the last comment, please don't original implementation such that using other stream operators is impossible.

📁 doc/reference/io/to_wkt.qbk

```diff
   2 |+   Boost.Geometry (aka GGL, Generic Geometry Library)
   3 |+ 
   4 |+   Copyright (c) 2009-2014 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: barendgehrels  
> Created_at: 2020-03-18 13:59:00 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394366083  

You can modify this to have your name here

> Username: sudo-panda  
> Created_at: 2020-03-19 15:11:36 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395101047  

How much of a file do I need to edit to add my Copyright?

> Username: mloskot  
> Created_at: 2020-03-19 15:12:49 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395101974  

3rd in the FAQ here https://www.boost.org/users/license.html

> Username: sudo-panda  
> Created_at: 2020-03-19 15:16:21 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395104714  

It says it's upto the author but what are your opinions on it?

> Username: mloskot  
> Created_at: 2020-03-19 15:19:54 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395107413  

There is no universal formula as this is highly case by case specific. I do this:  
- I make my own judgement  
- I add my copyright (or not)  
- I rely on reviewers to object (or not) if my change is eligible for the copyright update

> Username: sudo-panda  
> Created_at: 2020-03-19 15:21:15 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395108419  

Ok, thanks


📁 doc/src/examples/io/from_wkt.cpp

```diff
   2 |+ // QuickBook Example
   3 |+ 
   4 |+ // Copyright (c) 2014 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: barendgehrels  
> Created_at: 2020-03-18 13:59:10 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394366203  

You can modify this to have your name here


📁 include/boost/geometry/io/wkt/write.hpp

```diff
 117 |-     static inline void apply(std::basic_ostream<Char, Traits>& os, Point const& p, bool)
 163 |+     template <typename Output>
 164 |+     static inline void apply(Output& out, Point const& p, int significant_digits, bool)
```

> Username: barendgehrels  
> Created_at: 2020-03-18 22:52:40 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394684447  

No, please don't do this.  
  
There is more to stream than significant digits only.  
  
The original implementation should stay untouched, we should just be able to stream using any stream operator there is.  
  
I thought that a wrapper should be build on top, such that we could stream to a string. But that is not this implementation.

> Username: mloskot  
> Created_at: 2020-03-19 00:03:49 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r394707569  

@barendgehrels   
> No, please don't do this.  
  
Do you mean having the `int significant_digits`  which is ignored in all cases but `std::string`?  
  
> I thought that a wrapper should be build on top, such that we could stream to a string.   
  
Do you mean expected implementation is something like this?  
  
```cpp  
template <typename Geometry>  
inline std::string to_wkt(Geometry const& geometry, int significant_digits)  
{  
    std::stringstream ss;  
    ss.precision(significant_digits);  
    ss << boost::geometry::wkt(geometry);  
    return ss.str();  
}  
```

> Username: sudo-panda  
> Created_at: 2020-03-19 15:06:37 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395097173  

@mloskot that was my first proposal. But after that we agreed that it would be better create the string from the base and not just casting it from the stringstream to a string as mentioned [here](https://github.com/boostorg/geometry/issues/654#issuecomment-592458473). But in my opinion everybody should agree on one after hearing all arguments. So can you explicitly state what is the better approach according to you @barendgehrels ?

> Username: awulkiew  
> Created_at: 2020-03-19 17:53:35 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395214579  

@barendgehrels the stream-based implementation should not have been touched, hence my objection above about additional parameters to `bg::wkt()`.  
  
@barendgehrels could you clarify what is the exact problem with this PR?  
Is the problem that passing a dummy `significant_digits` into stream-based implementation is not elegant or that passing `significant_digits` into `bg::to_wkt()` at all is not elegant or something else?  
Is the argument that there should be more parameters in `bg::to_wkt()` or no parameters at all or that there should be no `bg::to_wkt()`?  
  
Note that if we had this simple version of `bg::to_wkt()` function mentioned by @mloskot then we'd have the same problems. It's only a matter of implementation.  
  
@barendgehrels how about removing `significant_digits` parameter and handle the coordinates always in a default way in `bg::to_wkt()`?

> Username: barendgehrels  
> Created_at: 2020-03-20 19:33:34 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395850627  

>   
>   
> @barendgehrels  
>   
> > No, please don't do this.  
>   
> Do you mean having the `int significant_digits` which is ignored in all cases but `std::string`?  
>   
> > I thought that a wrapper should be build on top, such that we could stream to a string.  
>   
> Do you mean expected implementation is something like this?  
>   
> ```c++  
> template <typename Geometry>  
> inline std::string to_wkt(Geometry const& geometry, int significant_digits)  
> {  
>     std::stringstream ss;  
>     ss.precision(significant_digits);  
>     ss << boost::geometry::wkt(geometry);  
>     return ss.str();  
> }  
> ```  
  
Yes, I had expected exactly this.

> Username: barendgehrels  
> Created_at: 2020-03-20 19:36:24 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395851879  

Thanks for your reactions. The significant_digits not the only issue, the problem is that I don't see a reason to change the implementation at all. If there are good reasons - OK of course. But if significant_digits is the reason, and streaming to a string - that is sooo easy to solve in a different way (see proposal of Mateusz) that I really don't see the reason to add this extra complexity.

> Username: barendgehrels  
> Created_at: 2020-03-20 19:45:48 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395856142  

>   
>   
> @mloskot that was my first proposal. But after that we agreed that it would be better create the string from the base and not just casting it from the stringstream to a string as mentioned [here](https://github.com/boostorg/geometry/issues/654#issuecomment-592458473). But in my opinion everybody should agree on one after hearing all arguments. So can you explicitly state what is the better approach according to you @barendgehrels ?  
  
First of all, @sudo-panda , thanks of course for your efforts! And sorry if my comment came in late an unexpectedly.  
  
To be honest, I missed this suggestion earlier, and I wonder why it would be better. Is the performance better? Both approaches use stringstream internally, so I don't expect significant differences. And adding significant_digits only quite limits it.  
  
OK - so you followed the suggestion and now I call you back... I like this comment of Mateusz there ("My rule of thumb is to always start with option that is the least intrusive to the existing code.") and I wonder why we didn't follow it...

> Username: awulkiew  
> Created_at: 2020-03-20 19:58:26 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r395861951  

@barendgehrels this was my suggestion based on the fact that I've made a simple benchmark and the result was that `std::string::append()` and `std::string::operator+=` was faster than `std::stringstream::operator<<` (as I mentioned on the Issue page). My test was rather synthetic but now we have an implementation so @sudo-panda could compare the speed of `bg::wkt` and `bg::to_wkt` and make a case that using `std::string` directly is indeed faster, or not.  
  
My additional reasoning was/is as follows:  
1. The simple function above is so trivial that the user could implement it using whatever stream manipulators that he/she needs. If we want to add a function like `to_wkt`, it only makes sense for me if our implementation could in some way beat this trivial user-level implementation, e.g. was faster.  
2. If we provide a support for some kind of output then we should use the native way of using this kind of output.  
3. Adding a layer of abstraction above appending data to streams and strings is of little complexity WRT what we already have in the library.

> Username: sudo-panda  
> Created_at: 2020-03-22 12:41:36 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396089099  

So which design are we going with?

> Username: barendgehrels  
> Created_at: 2020-03-22 13:23:09 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396093213  

>   
>   
> So which design are we going with?  
  
@awulkiew  thanks for your explanation. I remember now you mentioned this before, but I didn't realize it would have these consequences.  
  
@sudo-panda could you compare the speed between the two implementations as @awulkiew  suggested?  
  
Given all discussions, if you are all in favour of this new implementation (but there were still some discussions about the significant digits), I'm not against and you can go ahead.

> Username: sudo-panda  
> Created_at: 2020-03-22 13:24:54 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396093350  

Sure. I would love to benchmark them.

> Username: awulkiew  
> Created_at: 2020-03-22 14:50:11 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396102105  

@sudo-panda could you also benchmark alternative implementations of appending coordinates?  
  
If you want your benchmark to be quite full you could consider checking at least the following:  
```  
std::stringstream ss;  
ss.precision(std::numeric_limits<T>::digits10);  
ss << val;  
out += ss.str();  
```  
and  
```  
std::stringstream ss;  
// without precision  
ss << val;  
out += ss.str();  
```  
and  
```  
out += boost::lexical_cast<std::string>(val);  
```  
and  
```  
out += std::to_string(val);  
```  
  
All of the above for:  
1. geometry containing only coordinates having little number of significant digits, e.g. 0, 1, etc.  
2. geometry containing only coordinates having many significant digits, e.g. 0.333333333333, 0.666666666667, 1/3, 2/3, sqrt(2), pi, etc.  
  
All of the above for geometries having various numbers of coordinates/points in order to check different coordinate/character ratios in WKT strings, so e.g. to have several MULTIPOINTs containing 1, 10, 100 and 1000 points (at least 2 for 1 and 100?).  
  
So at least 16 combinations.

> Username: sudo-panda  
> Created_at: 2020-03-22 23:44:55 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396161707  

### Result:  
  
I made a heat map on plot.ly. You can access it [here](https://plot.ly/~sudo-panda/3/).  
  
Method Used | No of Points | Time Taken  
-------------------|------------------|----------------  
stringstream w precision, < sig dgts | 1 points| 0  
stringstream w precision, > sig dgts | 1 points| 3.2  
stringstream w precision, < sig dgts | 10 points| 26.86  
stringstream w precision, > sig dgts | 10 points| 26.16  
stringstream w precision, < sig dgts | 100 points| 249.81  
stringstream w precision, > sig dgts | 100 points| 265.87  
stringstream w precision, < sig dgts | 1000 points| 2496.11  
stringstream w precision, > sig dgts | 1000 points| 2616.56  
stringstream w precision, < sig dgts | 10000 points| 24895.1  
stringstream w precision, > sig dgts | 10000 points| 26237.3  
stringstream w/o precision, < sig dgts | 1 points| 0  
stringstream w/o precision, > sig dgts | 1 points| 3.37  
stringstream w/o precision, < sig dgts | 10 points| 66.75  
stringstream w/o precision, > sig dgts | 10 points| 59.92  
stringstream w/o precision, < sig dgts | 100 points| 273.41  
stringstream w/o precision, > sig dgts | 100 points| 219.3  
stringstream w/o precision, < sig dgts | 1000 points| 2625.64  
stringstream w/o precision, > sig dgts | 1000 points| 2816.33  
stringstream w/o precision, < sig dgts | 10000 points| 21972.1  
stringstream w/o precision, > sig dgts | 10000 points| 17633.8  
lexical cast, < sig dgts | 1 points| 0  
lexical cast, > sig dgts | 1 points| 1.73  
lexical cast, < sig dgts | 10 points| 15  
lexical cast, > sig dgts | 10 points| 64.22  
lexical cast, < sig dgts | 100 points| 600.01  
lexical cast, > sig dgts | 100 points| 379.06  
lexical cast, < sig dgts | 1000 points| 2525.91  
lexical cast, > sig dgts | 1000 points| 2541.71  
lexical cast, < sig dgts | 10000 points| 19197.2  
lexical cast, > sig dgts | 10000 points| 19841.5  
to_string, < sig dgts | 1 points| 0  
to_string, > sig dgts | 1 points| 1.02  
to_string, < sig dgts | 10 points| 11.17  
to_string, > sig dgts | 10 points| 9.03  
to_string, < sig dgts | 100 points| 129.17  
to_string, > sig dgts | 100 points| 125.14  
to_string, < sig dgts | 1000 points| 1065.07  
to_string, > sig dgts | 1000 points| 973.36  
to_string, < sig dgts | 10000 points| 9447.4  
to_string, > sig dgts | 10000 points| 8865.27  
  
  
  
> ***N.B.:*** Values for `< sig dgts` with just *one point* was giving time taken values as *9.41413e+11* so i set them to *0*.  
  
The code used to benchmark the methods :  
``` C++  
#include <iostream>  
#include <ctime>  
#include <cstdlib>  
#include <cmath>  
#include <chrono>   
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/multi_point.hpp>  
  
int impr_to_wkt(int no_of_points)  
{  
    namespace geom = boost::geometry;  
    typedef geom::model::d2::point_xy<double> point_type;  
  
    geom::model::multi_point<point_type> multi_pt;  
    for(int i = 0; i < no_of_points; i++)  
    {  
        srand( unsigned(i*time(NULL)));  
        geom::append(multi_pt, geom::make<point_type>(rand() % 1000 / 100.0, rand() % 1000 / 100.0));  
    }  
          
  
    std::string out;  
    std::chrono::_V2::system_clock::time_point start = std::chrono::high_resolution_clock::now();  
    out = geom::to_wkt(multi_pt);  
    std::chrono::_V2::system_clock::time_point stop = std::chrono::high_resolution_clock::now();  
    std::cerr<<out.length()<<"\t";  
  
    return std::chrono::duration_cast<std::chrono::microseconds>(stop - start).count();  
}  
  
int prec_to_wkt(int no_of_points)  
{  
    namespace geom = boost::geometry;  
    typedef geom::model::d2::point_xy<double> point_type;  
  
    geom::model::multi_point<point_type> multi_pt;  
    for(int i = 0; i < no_of_points; i++)  
    {  
        srand( unsigned(i*time(NULL)));  
        geom::append(multi_pt, geom::make<point_type>((double)rand()/(RAND_MAX), (double)rand()/(RAND_MAX)));  
    }  
      
    std::string out;  
    std::chrono::_V2::system_clock::time_point start = std::chrono::high_resolution_clock::now();  
    out = geom::to_wkt(multi_pt);  
    std::chrono::_V2::system_clock::time_point stop = std::chrono::high_resolution_clock::now();  
    std::cerr<<out.length()<<"\t";  
  
    return std::chrono::duration_cast<std::chrono::microseconds>(stop - start).count();  
}  
  
int main()  
{  
    for(int i = 0; i < 5; i++)  
    {  
        long long sum;  
        for(int j = 0 ; j<100; j++)  
        {  
            sum += impr_to_wkt(pow(10, i));  
        }  
        std::cout<<"stringstream w precision, "<<" < sig dgts, "<<(int) pow(10, i)<<" points, "<<sum/100.0<<std::endl;  
        sum = 0;  
        for(int j = 0 ; j<100; j++)  
        {  
            sum += prec_to_wkt(pow(10, i));  
        }  
        std::cout<<"stringstream w precision, "<<" > sig dgts, "<<(int) pow(10, i)<<" points, "<<sum/100.0<<std::endl;  
    }  
}  
```  
  
### Inference:  
Time taken by `to_string` is the least followed by `lexical_cast`. The `stringstream` method is the slowest When the precision is set, it makes it slower.

> Username: awulkiew  
> Created_at: 2020-03-23 01:28:33 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396175548  

@sudo-panda thanks! I don't see the benchmark for `bg::wkt()`. Have you tested it too?  
  
Do I see correctly that stringstream with precision set to digits10 is faster than the one without it? That's wierd.

> Username: awulkiew  
> Created_at: 2020-03-23 01:30:53 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396175887  

What compiler version and options have you used?

> Username: sudo-panda  
> Created_at: 2020-03-23 13:26:58 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396448391  

> I don't see the benchmark for `bg::wkt()`. Have you tested it too?  
  
I haven't tested it.  
  
> Do I see correctly that stringstream with precision set to digits10 is faster than the one without it? That's wierd.  
  
No `stringstream` with precision is slower than the one without as I said:  
  
>Time taken by `to_string` is the least followed by `lexical_cast`. The `stringstream` method is the slowest When the precision is set, it makes it slower.  
  
It can also be seen in the heat map provided.  
  
> What compiler version and options have you used?  
  
I have used the default `b2` configuration which uses `gcc-7` in my case. As for the options can you tell me how can I find that?

> Username: mloskot  
> Created_at: 2020-03-23 19:49:44 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396716350  

@sudo-panda  
>  As for the options can you tell me how can I find that?  
  
You definitally want to benchmark **optimised** build, not debug (which is default), that is:  
  
```  
b2 variant=release address-model=32  
b2 variant=release address-model=64  
```  
  
More options at https://boostorg.github.io/build/manual/develop/index.html#bbv2.overview.invocation.properties

> Username: awulkiew  
> Created_at: 2020-03-23 21:51:46 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396778534  

@sudo-panda  
  
> No stringstream with precision is slower  
  
Only for geometries with 10000 points and 100 > sig dgts.  
  
> I have used the default b2 configuration  
  
Ok, so the results are irrelevant for now.  
  
I suggest to NOT use b2 at all and to build your program directly with a compiler and pass -O2 or -O3 option explicitly.

> Username: mloskot  
> Created_at: 2020-03-23 22:03:45 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396784110  

> I suggest to NOT use b2 at all and to build your program directly with a compiler and pass -O2 or -O3 option explicitly.  
  
I'm not advocating for b2, but for completeness of the picture, it is also allows that:  
  
```  
b2 variant=release address-model=64 optimization=space  
b2 variant=release address-model=64 optimization=speed  
b2 variant=release address-model=64 optimization=full  
```  
  
If you want to see actual compiler command line invoked, just add `-d2` as in `b2 -d2 variant=release ....`

> Username: mloskot  
> Created_at: 2020-03-24 00:53:17 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396844465  

I have added `wkt` and `to_wkt` operations to my string benchmark in https://github.com/mloskot/string_benchmark/blob/master/benchmark_boost_geometry_wkt.cpp, where  
  
- two calls of `std::to_string(double)` for X and Y is the baseline  
- problem space means number of points (pairs of X/Y)  
  
### GCC 9  
  
CSV files with results: https://github.com/mloskot/string_benchmark/blob/master/results/gcc9_geometry_wkt.csv  
  
![image](https://user-images.githubusercontent.com/80741/77376552-decd9080-6d70-11ea-8987-209005401656.png)  
  
### MSVC++ 14.25 (VS2019)  
  
CSV files with results: https://github.com/mloskot/string_benchmark/blob/master/results/vs2019_geometry_wkt.csv  
  
![image](https://user-images.githubusercontent.com/80741/77376810-8f3b9480-6d71-11ea-9be6-b64d88dc69fb.png)  
  
------  
  
I picked up @awulkiew's theme on speeding things up because I think speed is important here. Adam's suggestion to re-work the internals opened possibility to completely avoid using streams for `to_wkt` and use of `std:;string` only, even with size `reserve`-ed heuristically based on number of points and dimension of geometry to further tweak performance (along with measuring it, of course). I hope this helps.

> Username: sudo-panda  
> Created_at: 2020-03-24 06:47:51 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r396931807  

What is the difference in `wkt` and `stringstream` experiments?  
  
And thanks for the extensive benchmark.

> Username: mloskot  
> Created_at: 2020-03-24 09:20:49 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r397005627  

@sudo-panda  
> What is the difference in `wkt` and `stringstream` experiments?  
  
Check the very _trivial_ code of [benchmark_boost_geometry_wkt.cpp](https://github.com/mloskot/string_benchmark/blob/master/benchmark_boost_geometry_wkt.cpp), you will see immediately. The `to_string`, `stringstream` and `lexical_cast` are generic cases outputting **N** points then `wkt` and `to_wkt` do similar for `multi_point` of **N** points, where  **N** is the **Problem Space** number of points (pairs of X/Y). When you compare results, compare test cases of the the same problem spaces.

> Username: sudo-panda  
> Created_at: 2020-03-24 11:19:53 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r397077107  

Ok I got confused thinking that you were using them in the `output_formatter` rather than benchmarking them standalone.

> Username: mloskot  
> Created_at: 2020-03-24 11:26:26 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r397080915  

No, I did not apply any modifications to the Boost.Geometry (code of this PR) at all.  
  
I just wrote quick benchmark which just compares  
  
1. 3 x basic general-purpose methods to output list of `double`-s to string methods: `to_string`, `stringstream` and `lexical_cast`  
2. 2 x Boost.Geometry methods: `wkt` and `to_wkt`  
  
I think of the 1. as reference results and I picked `to_string` as the single baseline to contrast all other methods against..  
I think of the 2. as results of interest for further optimisation.  
  
So, next, the benchmark could be completed with the Geometry's methods further tweaked, optimised, etc.

> Username: sudo-panda  
> Created_at: 2020-03-26 19:18:38 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r398830590  

Sorry for the late reply.  
  
I tried celero for the first time and the tool is really great although I don't know what to make of the varied results I got.  
  
I have uploaded my code to my repo: https://github.com/sudo-panda/bg_wkt_benchmark  
  
The compiler I have used is GNU C++ 7.5.0.  
  
I have also tried both O2 and O3 and an unoptimized version(as stated in celero docs)  
  
You can find the results of the respective operations in the results directory of my repo. I have also formatted it and sorted it in a google sheet and you can find that here: https://docs.google.com/spreadsheets/d/1AYqr0Unob5Ee6_-6ThEg3Z3iROzOuE01tmLQ46_JJis/edit?usp=sharing

> Username: mloskot  
> Created_at: 2020-03-27 11:44:37 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r399207309  

@sudo-pand I did just quick look, but I'm not sure.  
I would suggest you to let Celero do the statistics, remove `30` and stick `0`, then bump the `1` to more meaningful size.  You will also notice that I deliberately use `0` for number of iterations in `ExperimentValues`. Another thing is that I'm not sure about Celero's statistics when you force baseline to no-op. If you want to control benchmark with hard-wired runs, iterations, etc. I'd suggest you to use https://github.com/google/benchmark or https://github.com/nickbruun/hayai instead. Benchmarking needs time. My variant is simple and I think it gives quite clear results, but it does not include all test cases we may be interested in.

> Username: sudo-panda  
> Created_at: 2020-04-01 18:18:26 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r401816305  

I made the changes you suggested but I still can't find the method that is clearly better than the others. The results are a bit mixed. The changes are uploaded to the develop branch.   
  
https://github.com/sudo-panda/bg_wkt_benchmark/tree/develop  
  
I also updated the google sheet with new results:  
  
https://docs.google.com/spreadsheets/d/1AYqr0Unob5Ee6_-6ThEg3Z3iROzOuE01tmLQ46_JJis/edit?usp=sharing

> Username: mloskot  
> Created_at: 2020-04-27 22:58:12 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r416205496  

@sudo-panda I posted benchmarks update in the main thread of comments here https://github.com/boostorg/geometry/pull/670#issuecomment-620277337


---

## Comment 26

> Username: barendgehrels  
> Created_at: 2020-03-18 22:54:01 UTC  
> Updated_at: 2020-03-22 13:24:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-600898536  

~To be clear, please don't merge this. It is not good.~  
I scratch this, in line with my last comment

---

## Comment 27

> Username: awulkiew  
> Created_at: 2020-03-20 20:38:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-601900682  

@barendgehrels  
  
> Both approaches use stringstream internally, so I don't expect significant differences.   
  
Only for coordinates. For characters like `','` and native strings like `"MULTIPOLYGON("` `std::stringstream` is not used but the characters are directly appended to `std::string`. And nothing stands in the way of detecting native coordinate types and using e.g. C++11 `std::to_string` for them. I proposed to use `std::stringstream` only in case some user had his own user-defined coordinate type and had `operator<<` defined for this coordinate type, then it would be supported out of the box.  
  
Note that the above means that we should probably get rid of `significant_digits` parameter even for `std::string` because some output methods may not support it like e.g. Streams do. E.g. standard functions does not take this parameter as well as `boost::lexical_cast`.  
  
> And adding significant_digits only quite limits it.  
  
This is only an unelegant implementation where this variable is passed for both Strings and Streams but is ignored by the latter. See the default one for stream:  
https://github.com/boostorg/geometry/blob/3c62182c82e294d3446173187126ffb0ef6cf513/include/boost/geometry/io/wkt/write.hpp#L73  
  
The more "proper" way would be to e.g. pass `OutputPolicy` into `devarianted_wkt` and this policy would either be empty for Streams or contained whatever data is needed for Strings. But as I mentioned above I think we should consider simply removing this parameter altogether because we may decide to use something different for coordinates in case of Strings.

---

## Comment 28

> Username: mloskot  
> Created_at: 2020-04-27 22:54:03 UTC  
> Updated_at: 2020-04-28 09:33:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-620277337  

(I'm moving the thread https://github.com/boostorg/geometry/pull/670#discussion_r401816305 to main stream of comments as I need more horizontal space :))  
  
### Benchmark update  
  
@sudo-panda I played a bit more with benchmarking to better understand where your implementation sits versus the stream manipulator `wkt` and `to_wkt` as the basic wrapper.  
  
First, I defined `to_wkt` as the basic wrapper according to what we discussed with @barendgehrels in https://github.com/boostorg/geometry/pull/670#discussion_r394707569  
  
```cpp  
template <typename Geometry>  
inline std::string to_wkt_wrapper(Geometry const& geometry)  
{  
    std::stringstream ss;  
    ss << boost::geometry::wkt(geometry);  
    return ss.str();  
}  
template <typename Geometry>  
inline std::string to_wkt_wrapper(Geometry const& geometry, int significant_digits)  
{  
    std::stringstream ss;  
    ss.precision(significant_digits);  
    ss << boost::geometry::wkt(geometry);  
    return ss.str();  
}  
```  
  
then I added that to my [benchmark_boost_geometry_wkt.cpp](https://github.com/mloskot/string_benchmark/blob/master/benchmark_boost_geometry_wkt.cpp) as `to_wkt_wrap`.  
  
Here is the result:  
  
```  
|     Group      |   Experiment    |   Prob. Space   |     Samples     |   Iterations    |    Baseline     |  us/Iteration   | Iterations/sec  |  
|:--------------:|:---------------:|:---------------:|:---------------:|:---------------:|:---------------:|:---------------:|:---------------:|  
|wkt             | to_string       |               1 |              30 |          262144 |         1.00000 |         0.90276 |      1107714.67 |  
|wkt             | to_string       |             256 |              30 |              16 |         1.00000 |       245.25000 |         4077.47 |  
|wkt             | to_string       |             512 |              30 |               8 |         1.00000 |       490.37500 |         2039.26 |  
|wkt             | to_string       |            1024 |              30 |               4 |         1.00000 |       982.00000 |         1018.33 |  
|wkt             | stringstream    |               1 |              30 |          131072 |         1.66540 |         1.50346 |       665134.15 |  
|wkt             | stringstream    |             256 |              30 |              16 |         1.26988 |       311.43750 |         3210.92 |  
|wkt             | stringstream    |             512 |              30 |               8 |         1.24369 |       609.87500 |         1639.68 |  
|wkt             | stringstream    |            1024 |              30 |               4 |         1.25305 |      1230.50000 |          812.68 |  
|wkt             | lexical_cast    |               1 |              30 |          131072 |         1.76430 |         1.59274 |       627847.71 |  
|wkt             | lexical_cast    |             256 |              30 |              16 |         1.40979 |       345.75000 |         2892.26 |  
|wkt             | lexical_cast    |             512 |              30 |               8 |         1.40836 |       690.62500 |         1447.96 |  
|wkt             | lexical_cast    |            1024 |              30 |               4 |         1.40860 |      1383.25000 |          722.94 |  
|wkt             | wkt             |               1 |              30 |          131072 |         1.99188 |         1.79819 |       556115.61 |  
|wkt             | wkt             |             256 |              30 |              16 |         1.49847 |       367.50000 |         2721.09 |  
|wkt             | wkt             |             512 |              30 |               8 |         1.41601 |       694.37500 |         1440.14 |  
|wkt             | wkt             |            1024 |              30 |               4 |         1.40657 |      1381.25000 |          723.98 |  
|wkt             | to_wkt_wrap     |               1 |              30 |          131072 |         1.96920 |         1.77772 |       562519.04 |  
|wkt             | to_wkt_wrap     |             256 |              30 |              16 |         1.42635 |       349.81250 |         2858.67 |  
|wkt             | to_wkt_wrap     |             512 |              30 |               8 |         1.43946 |       705.87500 |         1416.68 |  
|wkt             | to_wkt_wrap     |            1024 |              30 |               4 |         1.39282 |      1367.75000 |          731.13 |  
|wkt             | to_wkt_wrap_3di |               1 |              30 |          131072 |         1.90817 |         1.72262 |       580511.72 |  
|wkt             | to_wkt_wrap_3di |             256 |              30 |              16 |         1.33384 |       327.12500 |         3056.94 |  
|wkt             | to_wkt_wrap_3di |             512 |              30 |               8 |         1.32858 |       651.50000 |         1534.92 |  
|wkt             | to_wkt_wrap_3di |            1024 |              30 |               4 |         1.32612 |      1302.25000 |          767.90 |  
|wkt             | to_wkt_wrap_9di |               1 |              30 |          131072 |         3.01452 |         2.72139 |       367459.31 |  
|wkt             | to_wkt_wrap_9di |             256 |              30 |              16 |         1.56600 |       384.06250 |         2603.74 |  
|wkt             | to_wkt_wrap_9di |             512 |              30 |               8 |         1.53887 |       754.62500 |         1325.16 |  
|wkt             | to_wkt_wrap_9di |            1024 |              30 |               4 |         1.56263 |      1534.50000 |          651.68 |  
|wkt             | to_wkt          |               1 |              30 |           65536 |         2.93074 |         2.64575 |       377964.38 |  
|wkt             | to_wkt          |             256 |              30 |              16 |         2.43552 |       597.31250 |         1674.17 |  
|wkt             | to_wkt          |             512 |              30 |               8 |         2.44099 |      1197.00000 |          835.42 |  
|wkt             | to_wkt          |            1024 |              30 |               4 |         2.41802 |      2374.50000 |          421.14 |  
|wkt             | to_wkt_3dig     |               1 |              30 |          131072 |         1.99573 |         1.80167 |       555041.75 |  
|wkt             | to_wkt_3dig     |             256 |              30 |              16 |         1.84174 |       451.68750 |         2213.92 |  
|wkt             | to_wkt_3dig     |             512 |              30 |               8 |         1.83533 |       900.00000 |         1111.11 |  
|wkt             | to_wkt_3dig     |            1024 |              30 |               4 |         1.83656 |      1803.50000 |          554.48 |  
|wkt             | to_wkt_9dig     |               1 |              30 |          131072 |         2.87525 |         2.59566 |       385258.86 |  
|wkt             | to_wkt_9dig     |             256 |              30 |              16 |         2.00739 |       492.31250 |         2031.23 |  
|wkt             | to_wkt_9dig     |             512 |              30 |               8 |         2.00408 |       982.75000 |         1017.55 |  
|wkt             | to_wkt_9dig     |            1024 |              30 |               4 |         2.00178 |      1965.75000 |          508.71 |  
```  
  
Next, I tried a basic optimisation: do not use `std::stringstream` if (theoretically) not necessary. Here is my version of `output_formatter<std::string>::append<T>`:  
  
```cpp  
template <typename T>  
static void append(std::string& out, T const& val, int significant_digits = -1)  
{  
    BOOST_STATIC_ASSERT((boost::is_arithmetic<T>::value));  
    if (significant_digits >= 0)  
    {  
        std::stringstream ss;  
        ss.precision(significant_digits);  
        ss << val;  
        out += ss.str();  
    }  
    else  
    {  
        out += std::to_string(val);  
    }  
}  
```  
  
Now, look at how this speeds up your `to_wkt` for the case of the default number of digits against the `to_wkt_wrap` from the results above as well as against `to_wkt_3dig` and `to_wkt_9dig` which go via `std::stringstream` for non-default number of digits:  
  
```  
|wkt             | to_wkt          |               1 |              30 |          131072 |         1.77079 |         1.64599 |       607537.67 |  
|wkt             | to_wkt          |             256 |              30 |              16 |         1.04082 |       255.00000 |         3921.57 |  
|wkt             | to_wkt          |             512 |              30 |               8 |         1.03724 |       508.25000 |         1967.54 |  
|wkt             | to_wkt          |            1024 |              30 |               4 |         1.03931 |      1018.00000 |          982.32 |  
|wkt             | to_wkt_3dig     |               1 |              30 |          131072 |         1.94660 |         1.80941 |       552666.31 |  
|wkt             | to_wkt_3dig     |             256 |              30 |              16 |         1.85459 |       454.37500 |         2200.83 |  
|wkt             | to_wkt_3dig     |             512 |              30 |               8 |         1.92270 |       942.12500 |         1061.43 |  
|wkt             | to_wkt_3dig     |            1024 |              30 |               4 |         1.91756 |      1878.25000 |          532.41 |  
|wkt             | to_wkt_9dig     |               1 |              30 |          131072 |         2.82303 |         2.62407 |       381087.51 |  
|wkt             | to_wkt_9dig     |             256 |              30 |              16 |         2.02883 |       497.06250 |         2011.82 |  
|wkt             | to_wkt_9dig     |             512 |              30 |               8 |         2.01913 |       989.37500 |         1010.74 |  
|wkt             | to_wkt_9dig     |            1024 |              30 |               4 |         2.01174 |      1970.50000 |          507.49 |  
```  
  
Note, it's enough to compare the `Baseline` values for corresponding problem space.  
  
### Observations  
  
1. Your current `to_wkt` makes lots of calls to `std::stringstream::str()`, while the `to_wkt_wrap` makes single call to `str()` at the end, that makes the former much slower.  
2. The modified `to_wkt_` using `to_string` is fast becasue `to_string` calls `sprintf` (or equivalent) and not `stringstream`.  
  
Regarding the 2., on MSVC++, it calls `scprintf` to get the required length, then constructs `string s(length, '\0')` as buffer, then `sprintf(&s[0], length + 1, "%f", value)` to convert `value` into string buffer `s`.  
  
### Conclusion  
  
If `output_formatter<std::string>` is implemented in terms of `sprintf` it will offer best performance which is also **~50% better performance** than `to_wkt_wrap` implemented as thin wrapper on top of the `wkt` stream manipulator.  
  
Assuming my results can be reproduced by others, I think such well performing implementation could justify the choice of more complex implementation behind `to_wkt` using the `output_formatter<std::string>` specialisation.  
  
Folks, please, let me know if my reasoning is correct or not.

---

## Comment 29

> Username: sudo-panda  
> Created_at: 2020-04-28 18:04:50 UTC  
> Updated_at: 2020-04-28 18:05:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-620766900  

@mloskot for now I am making `to_wkt` into a wrapper as it seems faster. As for the design of `output_formatter ` I will put another PR if other reviewers agree to follow through.  
  
And thanks for putting in so much effort. :)

---

## Comment 30

> Username: mloskot  
> Created_at: 2020-04-28 18:20:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-620775128  

@sudo-panda   
> for now I am making to_wkt into a wrapper as it seems faster.   
  
Yes, I personally think, a solution that is not slower than the stream manipulator is good enough.  
  
Optimising with `sprintf`-like way to avoid `stringstream` would require more work.

---

## Comment 31

> Username: sudo-panda  
> Created_at: 2020-09-11 10:35:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-691018201  

@barendgehrels , @awulkiew could you please review it so that it can be merged.

---

## Review 32 [Approved]

> Username: mloskot  
> Created_at: 2020-12-01 11:20:54 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-541799189  

AFAICT, this has now become a small addition that is not intrusive to the existing WKT I/O interface and operations, so I think it can be merged.  
  
/cc @barendgehrels @awulkiew @vissarion @sudo-panda

---

## Comment 33

> Username: barendgehrels  
> Created_at: 2020-12-02 15:26:03 UTC  
> Updated_at: 2020-12-02 15:35:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-737301469  

>   
>   
> AFAICT, this has now become a small addition that is not intrusive to the existing WKT I/O interface and operations, so I think it can be merged.  
>   
> /cc @barendgehrels @awulkiew @vissarion @sudo-panda  
  
Yes, it's not intrusive so that is a good thing.  
  
But I'm wondering what it adds now. On top of of `std::cout << bg::wkt(x);` which was there for 10 years, a stream operator as it should be, with all its flexibility in terms of precision, fixed/exponential, width, etc, we get a `bg::to_wkt(x,3)` now which works indirectly (using a stringstream) and only supports precision. What does this add to the library? Why having two alternatives for the same functionality? Isn't that confusing?  
  
And if not, shouldn't we build that then too for `dsv`, for example? (In my opinion not).  
  
With respect to the `from` variant I: having a `x = from_wkt<Type>(s)` might be useful for some users, it can be written on one line instead of two (though the documentation doesn't provide this). But also here the question: is that worth to offer an additional variant? You can say: yes, because we also have that for `envelope`, for example. But there (and for some other functions) we use the convention `return_envelope` .  
  
~So that would mean this should be called `return_wkt`. That would be acceptable for me.~ Strike that, it doesn't return a wkt but a geometry... OK, I guess then `from_wkt` would be fine.

---

## Comment 34

> Username: sudo-panda  
> Created_at: 2020-12-02 15:38:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-737310125  

> So that would mean this should be called `return_wkt`. That would be acceptable for me.  
  
@barendgehrels Is that the only change required for this PR to be approved by you?

---

## Review 35 [Commented]

> Username: barendgehrels  
> Created_at: 2020-12-02 18:37:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-542953075  

>   
>   
> > So that would mean this should be called `return_wkt`. That would be acceptable for me.  
>   
> @barendgehrels Is that the only change required for this PR to be approved by you?  
  
Note, I scratched that comment right after. So `from_wkt` is still acceptable, `return_wkt` would be weird because it doesn't return the wkt itself (`return_envelope` for example does). Sorry for the confusion.  
  
The PR is acceptable for me if `to_wkt` is removed, unless other reviewers find it a useful addition.

📁 doc/src/examples/io/from_wkt.cpp

```diff
  34 |+     e = boost::geometry::from_wkt<segment_type>("SEGMENT(1 0,3 4)");
  35 |+     c = boost::geometry::from_wkt<polygon_type>("POLYGON((0 0,0 7,4 2,2 0,0 0))");
  36 |+     b = boost::geometry::from_wkt<linestring_type>("LINESTRING(0 0,2 2,3 1)");
```

> Username: barendgehrels  
> Created_at: 2020-12-02 15:28:36 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r534256919  

See my other comment. If you want to make the advantage of another function clear, you should have that in the examples too. So:  
  
`auto const a = boost::geometry::from_wkt<point_type>("POINT(1 2)");`  
etc

---

📁 doc/src/examples/io/from_wkt.cpp

```diff
  19 |+ int main()
  20 |+ {
  21 |+     typedef boost::geometry::model::d2::point_xy<double> point_type;
```

> Username: barendgehrels  
> Created_at: 2020-12-02 15:29:31 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r534257753  

We should update old examples but let's at least new functionality give modernized style.  
So `using point_type = boost::geometry::...`  
etc


📁 doc/src/examples/io/to_wkt.cpp

```diff
  43 |+     std::cout << boost::geometry::to_wkt(point_frac, 3) << std::endl;
  44 |+     std::cout << boost::geometry::to_wkt(polygon_frac, 3) << std::endl;
  45 |+ 
```

> Username: barendgehrels  
> Created_at: 2020-12-02 15:31:03 UTC  
> Updated_at: 2020-12-03 11:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r534259083  

Thanks for the efforts of course. But I'm not sure about this example.  
Mainly because I'm not sure about the whole functionality at all.

> Username: sudo-panda  
> Created_at: 2020-12-03 11:43:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#discussion_r535148246  

I am waiting for comments from other reviewers before I change anything w.r.t. `to_wkt`.


---

## Review 36 [Commented]

> Username: vissarion  
> Created_at: 2020-12-07 11:21:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/670#pullrequestreview-546054993  

I am also OK with merging most of this PR but (as @barendgehrels ) I do not understand exactly what  `to_wkt` adds on top of the stream operator.

---

## Comment 37

> Username: awulkiew  
> Created_at: 2020-12-07 13:38:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-739923762  

@barendgehrels @vissarion This PR implements this issue: https://github.com/boostorg/geometry/issues/654

---

## Comment 38

> Username: vissarion  
> Created_at: 2020-12-10 15:00:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-742574564  

> @barendgehrels @vissarion This PR implements this issue: #654  
  
Right, thanks! So it seems there is user need so I will not go against. I am OK with this PR.

---

## Comment 39

> Username: mloskot  
> Created_at: 2020-12-10 16:11:01 UTC  
> Updated_at: 2020-12-10 16:12:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-742619149  

@vissarion, @barendgehrels  
>   
> Right, thanks! So it seems there is user need so I will not go against. I am OK with this PR.  
  
Yes, there seem to be a need indeed.  
  
Although the lengthy discussion we've had on this PR may given an impression it's a big feature, it is not.  
It just a little thing that smoothens up a particular rough edge of the WKT I/O interface making it just a bit more expressive and fluent. Apparently, for users, every little helps :-)

---

## Comment 40

> Username: barendgehrels  
> Created_at: 2020-12-11 18:13:02 UTC  
> Updated_at: 2020-12-11 18:14:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-743346490  

>   
>   
> @vissarion, @barendgehrels  
>   
> > Right, thanks! So it seems there is user need so I will not go against. I am OK with this PR.  
>   
> Yes, there seem to be a need indeed.  
>   
> Although the lengthy discussion we've had on this PR may given an impression it's a big feature, it is not.  
> It just a little thing that smoothens up a particular rough edge of the WKT I/O interface making it just a bit more expressive and fluent. Apparently, for users, every little helps :-)  
  
Thanks for the link (I mean the one with issue #654) . Yes, I remember now and I was OK back then, so I'm still OK now. I will approve.

---

## Comment 41

> Username: awulkiew  
> Created_at: 2021-03-03 13:05:30 UTC  
> Updated_at: 2021-03-03 13:05:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/670#issuecomment-789700419  

I apologize it took so long. Thanks for this PR!

---
