# #474 Replace Boost.Variant with Boost.Variant2 [Merged]

> Username: sdebionne  
> Created at: 2020-04-06 07:55:13 UTC  
> Updated at: 2020-04-20 15:55:53 UTC  
> Merged at: 2020-04-17 17:25:20 UTC  
> Closed at: 2020-04-17 17:25:20 UTC  
> Url: https://github.com/boostorg/gil/pull/474  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Implements dynamic extension with Boost.Variant2 instead of Boost.Variant.  
  
### References  
  
Fixes #232  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Update/Add test case(s)  
- [x] Update doc(s)  
- [x] Update extension usage in I/O  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2020-04-07 13:15:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/474#pullrequestreview-389102937  

📁 include/boost/gil/extension/dynamic_image/any_image_view.hpp

```diff
 138 |-     using type = any_image_view<mp11::mp_transform<dynamic_step_view, Views>>;
 139 |+     using type = mp11::mp_rename<
 140 |+         mp11::mp_transform<dynamic_step_view, typename any_image_view<Views...>::mp_views_t>,
```

> Username: pdimov  
> Created_at: 2020-04-07 13:15:22 UTC  
> Updated_at: 2020-04-14 07:01:04 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r404798977  

This is unnecessary. mp_transform will work on any_image_view<Views...> directly. No need to go to mp_list and then back.

> Username: sdebionne  
> Created_at: 2020-04-07 13:32:51 UTC  
> Updated_at: 2020-04-14 07:01:04 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r404811601  

Thanks! Much cleaner.


---

## Comment 2

> Username: mloskot  
> Created_at: 2020-04-08 21:47:52 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611211999  

Thanks, I'm very much in favour of switching to the Boost.Variant2

---

## Comment 3

> Username: sdebionne  
> Created_at: 2020-04-09 16:30:53 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611623549  

Builds fine with MSVC 2019.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2020-04-09 16:32:49 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611624460  

@pdimov Any chance that this issue that seems to be specific to MSVC 2017 rings a bell?  
```  
boost\mp11\list.hpp(202): error C3200: 'B': invalid template argument for template parameter 'B', expected a class template  
```

---

## Comment 5

> Username: pdimov  
> Created_at: 2020-04-09 17:13:17 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611644660  

My guess is that it doesn't like  
```  
using view_t = mp11::mp_rename<detail::images_get_views_t<any_image>, any_image_view>;  
```  
either because it doesn't like `any_image` or `any_image_view`. I'll look into why.

---

## Comment 6

> Username: pdimov  
> Created_at: 2020-04-09 17:16:45 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611646399  

Hm no, it's actually this line:  
```  
template <typename ...Views>  
class any_image_view : public variant2::variant<Views...>  
{  
    using parent_t = variant2::variant<Views...>;  
  
public:      
    using const_t = mp11::mp_rename<detail::views_get_const_t<any_image_view>, any_image_view>;  
```  
The second `any_image_view` is interpreted as referring to the class (same as the first one).

---

## Comment 7

> Username: pdimov  
> Created_at: 2020-04-09 17:19:30 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611647835  

Since the result of `detail::view_get_const_t<any_image_view>` is already `any_image_view`, just drop the rename.  
  
And maybe use b2 instead of cmake in order to see the errors.  
  
```  
compile-c-c++ ..\..\bin.v2\libs\gil\test\extension\toolbox\get_num_bits.test\msv  
c-14.1\debug\asynch-exceptions-on\threading-multi\get_num_bits.obj  
cl : Command line warning D9025 : overriding '/W3' with '/W4'  
get_num_bits.cpp  
C:\boost-git\develop\boost/mp11/list.hpp(202): error C3200: 'B': invalid templat  
e argument for template parameter 'B', expected a class template  
        with  
        [  
            B=boost::gil::any_image_view<Views...>  
        ]  
C:\boost-git\develop\boost/gil/extension/dynamic_image/any_image_view.hpp(79): n  
ote: see reference to alias template instantiation 'boost::mp11::mp_rename<boost  
::gil::detail::views_get_const_t<boost::gil::any_image_view<Views...>>,boost::gi  
l::any_image_view<Views...>>' being compiled  
C:\boost-git\develop\boost/gil/extension/dynamic_image/any_image_view.hpp(122):  
note: see reference to class template instantiation 'boost::gil::any_image_view<  
Views...>' being compiled  
```

---

## Comment 8

> Username: pdimov  
> Created_at: 2020-04-09 17:20:40 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611648411  

```  
C:\boost-git\develop\libs\gil>git diff  
diff --git a/include/boost/gil/extension/dynamic_image/any_image_view.hpp b/include/boost/gil/extension/dynamic_image/any_image_view.hpp  
index 1119b2f7f..f4a2f7351 100644  
--- a/include/boost/gil/extension/dynamic_image/any_image_view.hpp  
+++ b/include/boost/gil/extension/dynamic_image/any_image_view.hpp  
@@ -76,7 +76,7 @@ class any_image_view : public variant2::variant<Views...>  
     using parent_t = variant2::variant<Views...>;  
  
 public:  
-    using const_t = mp11::mp_rename<detail::views_get_const_t<any_image_view>, any_image_view>;  
+    using const_t = detail::views_get_const_t<any_image_view>;  
     //using const_t = detail::views_get_const_t<any_image_view>;  
     using x_coord_t = std::ptrdiff_t;  
     using y_coord_t = std::ptrdiff_t;  
```

---

## Comment 9

> Username: pdimov  
> Created_at: 2020-04-09 17:21:24 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611648767  

I see that you already had the right thing there but commented out. :-)

---

## Comment 10

> Username: sdebionne  
> Created_at: 2020-04-10 06:45:16 UTC  
> Updated_at: 2020-04-10 07:23:40 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-611903832  

Thank you so much for your help, I am always amazed by the support you get from this community.  
  
Why on earth did I add this `mp_rename` ? :-)  
  
If I understand correctly, `mp_rename<A<T...>, A>` fails on MSVC 2017 or is it just with `any_image_view`?

---

## Comment 11

> Username: pdimov  
> Created_at: 2020-04-10 14:10:08 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-612044878  

It only fails when you're inside the definition of `template<class... T> class A`, because there `A` can refer to both the class itself (`A<T...>`, "class name injection") and the class template (`A`), and 2017 doesn't consider the second possibility. You were basically doing something similar to `mp_rename<A, A>`, where the two `A`s mean different things.

---

## Comment 12

> Username: sdebionne  
> Created_at: 2020-04-14 07:26:28 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-613272622  

One of the [Azure jobs](https://dev.azure.com/boostorg/gil/_build/results?buildId=915) failed with:  
  
```  
Cloning into 'boost-root'...  
C:/Program Files/Git/mingw64/libexec/git-core\git-submodule: line 457:  1643 Segmentation fault      git submodule--helper ensure-core-worktree "$sm_path"  
```  
  
Any chance that it could be restarted?

---

## Comment 13

> Username: mloskot  
> Created_at: 2020-04-14 08:27:00 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-613300054  

@sdebionne Restarted

---

## Review 14 [Approved]

> Username: mloskot  
> Created_at: 2020-04-15 20:12:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/474#pullrequestreview-394100204  

Awesome, thank you for contributions! For me, it's ready to merge. Let's give others a day or two to review.  
  
I think we could also get rid of this bridge now  
  
https://github.com/boostorg/gil/blob/a7a2cb282324e3e47329852076111808dd9f673e/include/boost/gil/detail/mp11.hpp#L13

---

## Review 15 [Commented]

> Username: simmplecoder  
> Created_at: 2020-04-15 20:34:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/474#pullrequestreview-394114276  

📁 include/boost/gil/extension/dynamic_image/apply_operation.hpp

```diff
  21 |+ auto apply_operation(variant2::variant<Types...>& arg, UnaryOp op)
  22 | #if defined(BOOST_NO_CXX14_DECLTYPE_AUTO) || defined(BOOST_NO_CXX11_DECLTYPE_N3276)
  23 |     -> typename UnaryOp::result_type
```

> Username: simmplecoder  
> Created_at: 2020-04-15 20:34:02 UTC  
> Updated_at: 2020-04-15 20:38:24 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r409116964  

Can we do `-> decltype(apply_visitor(op, arg);)` here? Ditto for other overloads. I believe `variant2` doesn't impose such a limitation, does it? If not, perhaps we could `declval`  our way through in a separate helper struct?

> Username: mloskot  
> Created_at: 2020-04-15 20:51:14 UTC  
> Updated_at: 2020-04-15 20:51:31 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r409126218  

@simmplecoder Only as `#else` case, I think. However, I'd suggest to do it as an enhancement in separate PR.

> Username: simmplecoder  
> Created_at: 2020-04-15 20:53:37 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r409127419  

@mloskot , `decltype` was added in C++11 [cppreference](https://en.cppreference.com/w/cpp/language/decltype). I can test that out, but only on weekends probably.

> Username: mloskot  
> Created_at: 2020-04-15 21:19:13 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r409140127  

@simmplecoder  Yes, but there is `BOOST_NO_CXX11_DECLTYPE` vs `BOOST_NO_CXX11_DECLTYPE_N3276` which I'm not sure about

> Username: sdebionne  
> Created_at: 2020-04-16 11:52:50 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r409495768  

My humble opinion is that `apply_operation` should be marked `[[deprecated]]` and provided only for backward compatibility. Just use `visit` in modern code (no limit on arity, etc...)

> Username: mloskot  
> Created_at: 2020-04-16 11:59:35 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r409499380  

I agree. There is lots of legacy constructs that can be deprecated and avoided now.   
It will take another refactoring overhaul, or two. I just didn't have time to move to that.

> Username: simmplecoder  
> Created_at: 2020-04-17 07:17:43 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r410036507  

I agree as well. I believe those apply functions also don't catch easy errors, like when one forgets `gil::view` or something like that, and due to upcast it is unclear why the functor cannot be called.

> Username: simmplecoder  
> Created_at: 2020-04-17 07:18:32 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r410036960  

@mloskot , I believe boost variant2 itself might depend on `BOOST_NO_CXX11_DECLTYPE_N3276`. I will try to find out if it is really the case.

> Username: mloskot  
> Created_at: 2020-04-17 17:22:13 UTC  
> Updated_at: 2020-04-17 17:22:14 UTC  
> Url: https://github.com/boostorg/gil/pull/474#discussion_r410363639  

I made a TODO from the whole idea https://github.com/boostorg/gil/issues/482


---

## Review 16 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-04-15 20:45:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/474#pullrequestreview-394121900  

This looks like a great improvement to the `dynamic_image` extension.  
I'm a bit wary, though, seeing that all our I/O extensions (and more !) depend on that. Wouldn't there be a way to split this, so someone can use the `PNG` reader without having to drag in `boost/variant2.hpp`, say ?

---

## Comment 17

> Username: mloskot  
> Created_at: 2020-04-15 20:48:25 UTC  
> Updated_at: 2020-04-15 21:34:00 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-614272193  

@stefanseefeld   
> Wouldn't there be a way to split this, so someone can use the PNG reader without having to drag in boost/variant2.hpp  
  
Currently, those all drag in `boost/variant.hpp`, so it's NOT adding a new dependency, but replacing an old A with new B. And, as the new B has less dependencies than the old A, it means removing some transitive dependencies. Unless I'm missing something.

---

## Comment 18

> Username: stefanseefeld  
> Created_at: 2020-04-15 21:33:09 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-614291258  

OK, that's a fair point. So let's move forward with this PR, and try to separate the `dynamic_image` extension dependency from the other extensions separately.

---

## Comment 19

> Username: mloskot  
> Created_at: 2020-04-15 21:36:44 UTC  
> Url: https://github.com/boostorg/gil/pull/474#issuecomment-614292662  

@stefanseefeld Yes, I think this PR is completed now and feasibility of decoupling the `dynamic_image` and `io` extensions should be a matter of separate discussion and PR.  
So, unless you have any other requests for changes, let's mark this as approved.

---
