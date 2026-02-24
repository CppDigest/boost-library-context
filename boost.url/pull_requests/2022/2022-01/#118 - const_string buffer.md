# #118 const_string buffer [Closed]

> Username: alandefreitas  
> Created at: 2022-01-25 06:48:34 UTC  
> Updated at: 2022-03-14 15:56:44 UTC  
> Closed at: 2022-01-27 03:13:09 UTC  
> Url: https://github.com/boostorg/url/pull/118  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-01-25 10:47:04 UTC  
> Updated_at: 2022-01-26 19:18:45 UTC  
> Url: https://github.com/boostorg/url/pull/118#issuecomment-1021052437  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#118](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (e0fe86e) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/9c41eb14ba7a17ea1b6507afcc1395e168199ea2?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (9c41eb1) will **decrease** coverage by `0.02%`.  
> The diff coverage is `96.55%`.  
  
> :exclamation: Current head e0fe86e differs from pull request most recent head a33ffd6. Consider uploading reports for the commit a33ffd6 to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/118/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #118      +/-   ##  
===========================================  
- Coverage    96.29%   96.27%   -0.03%       
===========================================  
  Files          103      104       +1       
  Lines         5455     5478      +23       
===========================================  
+ Hits          5253     5274      +21       
- Misses         202      204       +2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/impl/const\_string.ipp](https://codecov.io/gh/CPPAlliance/url/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaXBw) | `97.61% <93.10%> (-2.39%)` | :arrow_down: |  
| [include/boost/url/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvY29uc3Rfc3RyaW5nLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/impl/const\_string.hpp](https://codecov.io/gh/CPPAlliance/url/pull/118/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvaW1wbC9jb25zdF9zdHJpbmcuaHBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [9c41eb1...a33ffd6](https://codecov.io/gh/CPPAlliance/url/pull/118?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 17:02:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-862599103  

📁 test/unit/const_string.cpp

```diff
 215 |+                             dest[7] = 'h';
 216 |+                             dest[8] = 'i';
 217 |+                             dest[9] = 'j';
```

> Username: vinniefalco  
> Created_at: 2022-01-25 17:02:02 UTC  
> Updated_at: 2022-01-25 17:02:03 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791933100  

why not just  
```  
std::memcpy( dest, "abcdefghij", 10 );  
```  
?

> Username: alandefreitas  
> Created_at: 2022-01-25 17:55:39 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791980484  

I was trying to follow a pattern but you're right.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 17:03:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-862600639  

📁 include/boost/url/impl/const_string.ipp

```diff
 151 | const_string(
 152 |     const_string const& other) noexcept
 153 |     : string_view(other)
```

> Username: vinniefalco  
> Created_at: 2022-01-25 17:03:14 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791934194  

if you are going to completely initialize the object in the function body, then you don't need to delegate the construction. Also don't you need to call `p_->release()` in the else body?

> Username: alandefreitas  
> Created_at: 2022-01-25 17:59:55 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791983948  

> if you are going to completely initialize the object in the function body, then you don't need to delegate the construction.   
  
Oh... right. It's because I was only initializing in the function body when the condition was true. But that wasn't a good idea.  
  
> Also don't you need to call p_->release() in the else body?  
  
In the else body, there's nothing to release from other because other is using SSO. And there's nothing to release from this because it's still being constructed.

> Username: vinniefalco  
> Created_at: 2022-01-25 18:04:19 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791987402  

Ah right. it could use a comment :) do be aware of the rules for object destruction when a constructor throws. If the delegated constructor succeeds, and the body of the delegating constructor throws, then the object will have the destructor called.

> Username: alandefreitas  
> Created_at: 2022-01-25 18:11:13 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791992474  

Actually, I have to do both:  
  
```  
 warning: base class ‘class boost::core::basic_string_view<char>’ should be explicitly initialized in the copy constructor [-Wextra]  
```  
  
I'm including the comment.  
  
> a constructor throws  
  
I'm using a noexcept constructor in the initializer. But the constructor overload we are using for `string_view(other.data(), other.size())` is not noexcept. Should we remove noexcept from the copy constructor?

> Username: alandefreitas  
> Created_at: 2022-01-26 19:09:35 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792956786  

Same as another comment below


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 17:03:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-862601355  

📁 include/boost/url/impl/const_string.ipp

```diff
 192 |-     static_cast<string_view&>(
 193 |-         *this) = other;
 173 |+     if (other.size() > sizeof(base*)) {
```

> Username: vinniefalco  
> Created_at: 2022-01-25 17:03:45 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791934697  

Consider adding this private function:  
```  
constexpr bool const_string::is_small() const noexcept;  
```  
and calling it instead

> Username: alandefreitas  
> Created_at: 2022-01-25 18:05:28 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791988267  

That was my first instinct. But I thought it would be better to let you suggest what to do.


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 17:04:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-862602078  

📁 include/boost/url/impl/const_string.ipp

```diff
 178 |+         data_.p_ = other.data_.p_;
 179 |+         static_cast<string_view&>(*this) =
 180 |+             static_cast<const string_view&>(other);
```

> Username: vinniefalco  
> Created_at: 2022-01-25 17:04:18 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791935176  

won't this work?  
```  
static_cast<string_view&>(*this) = other;  
```

> Username: alandefreitas  
> Created_at: 2022-01-25 18:06:50 UTC  
> Updated_at: 2022-01-25 18:06:51 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r791989246  

It emits a warning about slicing.

> Username: vinniefalco  
> Created_at: 2022-01-25 19:41:23 UTC  
> Updated_at: 2022-01-25 19:41:24 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792059693  

fuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuck


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 19:29:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-862767236  

📁 include/boost/url/impl/const_string.hpp

```diff
 158 |-     init(n, r.data);
 159 |-     return s;
 156 |+     if (!is_small(n)) {
```

> Username: vinniefalco  
> Created_at: 2022-01-25 19:29:06 UTC  
> Updated_at: 2022-01-25 19:29:19 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792050973  

put the more likely condition first, e.g.  
```  
if( is_small(n) )  
  return const_string(  
    n, std::allocator<char>{}, init);  
auto r = p_->construct(n);  
const_string s(r);  
init(n, r.data);  
return s;  
```


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-25 19:43:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-862784225  

📁 test/unit/const_string.cpp

```diff
 164 |+                 {
 165 |+                     factory a;
 166 |+                     s = a(10,
```

> Username: vinniefalco  
> Created_at: 2022-01-25 19:43:13 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792060981  

You need to expose `sizeof(base*)` as a public constexpr constant so that tests can be written which guarantee going into small or dynamic storage, even if the size of the small buffer is changed.


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 16:55:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-863828608  

📁 include/boost/url/const_string.hpp

```diff
  62 |+     static constexpr
  63 |+     bool
  64 |+     is_small(std::size_t s) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:55:34 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792846792  

`n`


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 16:56:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-863829340  

📁 include/boost/url/const_string.hpp

```diff
  76 |+     std::size_t
  77 |+     buffer_size = buffer_size_;
  78 |+ 
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:56:08 UTC  
> Updated_at: 2022-01-26 16:56:09 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792847326  

move this to the top and remove `buffer_size_`

> Username: vinniefalco  
> Created_at: 2022-01-26 16:56:26 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792847600  

and bump the size up to 32 :)

> Username: alandefreitas  
> Created_at: 2022-01-26 18:57:42 UTC  
> Updated_at: 2022-01-26 18:57:43 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792948259  

> and bump the size up to 32 :)  
  
😎


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 16:56:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-863830062  

📁 include/boost/url/impl/const_string.hpp

```diff
 157 |+         return const_string(
 158 |+             n, std::allocator<char>{}, init);
 159 |+     } else {
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:56:40 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792847832  

flatten out the else


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 16:57:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-863830679  

📁 include/boost/url/impl/const_string.hpp

```diff
 179 |+             *this) = { data_.buf_, s.size() };
 180 |+     } else {
 181 |+         auto r = factory::impl<
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:57:08 UTC  
> Updated_at: 2022-01-26 16:57:09 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792848262  

flatten this scope by removing the else


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 16:57:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-863830777  

📁 include/boost/url/impl/const_string.hpp

```diff
 203 |+         static_cast<string_view&>(
 204 |+             *this) = { data_.buf_, size };
 205 |+     } else {
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:57:13 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792848329  

flatten this scope by removing the else


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 16:58:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-863832227  

📁 include/boost/url/impl/const_string.ipp

```diff
 178 |-     : string_view(other)
 179 |-     , p_(other.p_)
 153 |+     : string_view()
```

> Username: vinniefalco  
> Created_at: 2022-01-26 16:58:20 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792849405  

do we need this

> Username: alandefreitas  
> Created_at: 2022-01-26 19:08:41 UTC  
> Updated_at: 2022-01-26 19:08:42 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792956141  

Removing this would lead to a warning in some compilers  
  
```  
warning: base class ‘class boost::core::basic_string_view<char>’ should be explicitly initialized in the copy constructor  
```

> Username: alandefreitas  
> Created_at: 2022-01-26 19:09:18 UTC  
> Updated_at: 2022-01-26 19:09:19 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r792956568  

Same as another comment above

> Username: vinniefalco  
> Created_at: 2022-01-26 23:23:13 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793133156  

That's a weird warning, I'm not sure its valid. I thought the base would be implicitly default constructed?


---

## Comment 14

> Username: vinniefalco  
> Created_at: 2022-01-26 21:22:36 UTC  
> Url: https://github.com/boostorg/url/pull/118#issuecomment-1022614963  

Still not formatted according to spec

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2022-01-26 21:23:48 UTC  
> Url: https://github.com/boostorg/url/pull/118#issuecomment-1022615811  

Also this needs rebase to develop, and a javadoc for `buffer_size`

---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 23:19:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-864218579  

📁 include/boost/url/const_string.hpp

```diff
  44 | {
  45 |+ public:
  46 |+     /** Size of the string buffer
```

> Username: vinniefalco  
> Created_at: 2022-01-26 23:19:57 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793131658  

This is misleading because a user could think it means the "current" size. You could say "small buffer size" or "built-in capacity" or... idk, @pdimov ?


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 23:20:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-864218704  

📁 include/boost/url/const_string.hpp

```diff
  60 | 
  48 |-     base* p_;
  61 |+     union data_t {
```

> Username: vinniefalco  
> Created_at: 2022-01-26 23:20:10 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793131757  

these braces on the same line are going to be the death of me


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 23:20:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-864218897  

📁 include/boost/url/impl/const_string.hpp

```diff
 156 |+     if (is_small(n)) {
 157 |+         return const_string(
 158 |+             n, std::allocator<char>{}, init);
```

> Username: vinniefalco  
> Created_at: 2022-01-26 23:20:28 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793131903  

don't need braces if single statement


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 23:20:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-864219044  

📁 include/boost/url/impl/const_string.hpp

```diff
 170 |-     auto r = factory::impl<
 171 |-         Allocator>(a).construct(
 174 |+     if (is_small(s.size())) {
```

> Username: vinniefalco  
> Created_at: 2022-01-26 23:20:43 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793132004  

the brace


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 23:22:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-864220076  

📁 include/boost/url/impl/const_string.ipp

```diff
 170 |-         return &cs;
 171 |-     }())
 145 |+     : string_view(data_.buf_, 0)
```

> Username: vinniefalco  
> Created_at: 2022-01-26 23:22:28 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793132846  

would `string_view()` be better? idk


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-01-26 23:23:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/118#pullrequestreview-864220597  

📁 include/boost/url/impl/const_string.ipp

```diff
 181 |-     ++p_->refs;
 154 |+     if (is_small(other.size())) {
 155 |+         // other is small / nothing to release
```

> Username: vinniefalco  
> Created_at: 2022-01-26 23:23:24 UTC  
> Url: https://github.com/boostorg/url/pull/118#discussion_r793133236  

good comment


---
