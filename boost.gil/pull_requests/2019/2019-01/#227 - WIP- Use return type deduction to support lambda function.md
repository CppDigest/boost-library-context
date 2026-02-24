# #227 WIP: Use return type deduction to support lambda function [Closed]

> Username: sdebionne  
> Created at: 2019-01-29 10:20:48 UTC  
> Updated at: 2020-10-20 16:40:24 UTC  
> Closed at: 2019-02-05 08:30:59 UTC  
> Url: https://github.com/boostorg/gil/pull/227  

Allows to use gil variant (e.g. `any_image<>` ) visitation with generic lambdas:  
  
```  
gil::apply_operation(any_image_view<...>, [](auto view) {  
   // do something with the view  
});  
```  
  
### Tasklist  
  
- [ ] Add test case(s)  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] All CI builds and checks have passed

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-01-29 10:39:33 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/227#pullrequestreview-197479185  

📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
 215 |+ 
 216 |+ namespace detail {
 217 |+ 	template <std::size_t N> struct apply_operation_fwd_fn {};
```

> Username: mloskot  
> Created_at: 2019-01-29 10:31:21 UTC  
> Updated_at: 2019-01-29 10:39:33 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251775127  

> Do not increases the indentation level within namespace.  
https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

---

📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
 223 |+ // unary application
 224 |+ template <typename Types, typename Bits, typename Op>
 225 |+ auto BOOST_FORCEINLINE apply_operation_basec(const Bits& bits, std::size_t index, Op op) {
```

> Username: mloskot  
> Created_at: 2019-01-29 10:33:07 UTC  
> Updated_at: 2019-01-29 12:03:26 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251775819  

Could we try to keep the lines shorter, this way?  
  
```cpp  
BOOST_FORCEINLINE  
auto apply_operation_basec(const Bits& bits, std::size_t index, Op op)  
{  
    ...  
}  
```  
(@sdebionne I've just updated the snippet above)  
  
BTW, there have been some discussion about possibility to get rid of `BOOST_FORCEINLINE` one day.

---

📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
 234 |+ 
 235 |+ namespace detail {
 236 |+ 	template <typename T2, typename Op>
```

> Username: mloskot  
> Created_at: 2019-01-29 10:33:26 UTC  
> Updated_at: 2019-01-29 10:39:34 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251775950  

> Do not increases the indentation level within namespace.  
https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

---

📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
 262 |+ // Binary application by applying on each dimension separately
 263 |+ template <typename Types1, typename Types2, typename Bits1, typename Bits2, typename Op>
 264 |+ static auto BOOST_FORCEINLINE apply_operation_base(const Bits1& bits1, std::size_t index1, const Bits2& bits2, std::size_t index2, Op op) {
```

> Username: mloskot  
> Created_at: 2019-01-29 10:33:49 UTC  
> Updated_at: 2019-01-29 10:39:34 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251776090  

> Write your code to fit within 90 columns of text  
https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

---

📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
 169 |+ 
 170 |+ #define GIL_APPLY_FWD_OP(z, N, text)                                                                        \
 171 |+     template <> struct apply_operation_fwd_fn<BOOST_PP_ADD(N,1)> {                                      \
```

> Username: mloskot  
> Created_at: 2019-01-29 10:36:33 UTC  
> Updated_at: 2019-01-29 10:39:34 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251776968  

I once had a wish to get rid of `BOOST_PP_*` :)

---

📁 include/boost/gil/extension/dynamic_image/apply_operation_base.hpp

```diff
 182 |+         template <typename Types, typename Bits, typename UnaryOp>                                     \
 183 |+         auto applyc(const Bits& bits, std::size_t index, UnaryOp op) const { \
 184 |+             typedef typename mpl::begin<Types>::type                                             \
```

> Username: mloskot  
> Created_at: 2019-01-29 10:39:20 UTC  
> Updated_at: 2019-01-29 10:39:34 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251777968  

Would it be possible to replace all the MPL stuff with MP11 (http://boost.org/libs/mp11/) ?  
  
This is a new code and it would help if it does not introduce any more uses of MPL which I'm trying to get rid of.  
  
Even if MP11requires a bit of temporary plumbing e.g. to map `boost::{true|false}_` to `std::{true|false}_type`, I think it is worth to try.

> Username: sdebionne  
> Created_at: 2019-01-29 11:51:29 UTC  
> Updated_at: 2019-01-29 11:51:30 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251800628  

I can try but this is not really new code, it's mostly copy-and-paste and getting rid of the explicit return type, letting C++14 return type deduction do the work when available.

> Username: mloskot  
> Created_at: 2019-01-29 12:05:03 UTC  
> Updated_at: 2019-01-29 12:05:04 UTC  
> Url: https://github.com/boostorg/gil/pull/227#discussion_r251804672  

Right. I mean, it's worth a try, but I don't think it's a show-stopper, if you don't.


---

## Comment 2

> Username: sdebionne  
> Created_at: 2019-02-04 08:20:14 UTC  
> Url: https://github.com/boostorg/gil/pull/227#issuecomment-460163277  

I'll fix the formatting issues (actually I think I have a less redondant solution) but I have a question regarding the CI: are there any tests that run with std=C++14? My modifications are only active when `BOOST_NO_CXX14_RETURN_TYPE_DEDUCTION` is set and hence depends on that compile options.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-02-04 10:41:50 UTC  
> Url: https://github.com/boostorg/gil/pull/227#issuecomment-460201719  

@sdebionne   
>  Are there any tests that run with std=C++14?   
  
No, we currently don't have any builds for C++14 mode.  
  
I think it would be a good idea to add one, and for C++17 too.  
  
* Do you have any preference regarding CI service?  
* Do you need a specific compiler version? See table in the README for what we've got on which CI. On CircleCI, we have these available without any extra installation required:  
https://github.com/boostorg/gil/blob/7722f08c80c15e6844f7326767b94e1696bf7b24/.circleci/config.yml#L15-L75  
  
* Keep in mind, Travis CI usually starts building with looong delays.  
  
Let me know and I will add new build job, then you can update your PR.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2019-02-05 08:30:59 UTC  
> Url: https://github.com/boostorg/gil/pull/227#issuecomment-460552577  

Superseded by #231.  
  
@mloskot   
  
> Do you have any preference regarding CI service?  
  
Nope, whatever is easier for you.  
  
> Do you need a specific compiler version? See table in the README for what we've got on which CI. On CircleCI, we have these available without any extra installation required:  
  
According to [compiler support](https://en.cppreference.com/w/cpp/compiler_support), anything above gcc 5 is fully c++14 compliant. Personally I use gcc 7.3 as it's the one that ship with Conda but that is not a requirement.  
  
Azure pipelines look nice and reactive.

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-02-05 08:41:19 UTC  
> Url: https://github.com/boostorg/gil/pull/227#issuecomment-460555255  

OK, I will try to work it out today.

---
