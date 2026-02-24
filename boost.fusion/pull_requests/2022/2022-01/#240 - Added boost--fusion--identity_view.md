# #240 Added boost::fusion::identity_view [Merged]

> Username: denzor200  
> Created at: 2022-01-10 17:21:01 UTC  
> Updated at: 2022-07-04 10:28:57 UTC  
> Merged at: 2022-01-16 00:39:38 UTC  
> Closed at: 2022-01-16 00:39:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/240  

https://github.com/boostorg/fusion/issues/233

---

## Comment 1

> Username: denzor200  
> Created_at: 2022-01-10 17:26:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#issuecomment-1009156382  

`error C2903: 'result' : symbol is neither a class template nor a function template`  
it seems like `boost::identity` doesn't support C++03

---

## Comment 2

> Username: djowel  
> Created_at: 2022-01-11 01:37:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#issuecomment-1009521707  

> `error C2903: 'result' : symbol is neither a class template nor a function template`  
> it seems like `boost::identity` doesn't support C++03  
  
AFAICT, `identity` is very simple. You can just implement it yourself in a detail namespace. I wouldn't be surprised if fusion already has it somewhere.

---

## Review 3 [Commented]

> Username: denzor200  
> Created_at: 2022-01-13 03:06:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/fusion/pull/240#pullrequestreview-848122860  

📁 test/sequence/identity_view.cpp

```diff
 144 |+         typedef boost::fusion::result_of::next<second>::type third;
 145 |+ 
 146 |+         // TODO: why is a boost::fusion::pair<int, char>&& ??
```

> Username: denzor200  
> Created_at: 2022-01-10 17:23:48 UTC  
> Updated_at: 2022-01-13 03:06:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r781396281  

make out

> Username: denzor200  
> Created_at: 2022-01-13 03:05:39 UTC  
> Updated_at: 2022-01-13 03:06:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r783597682  

The problem is that `identity` will never return a value, it will only return a reference. Moreover - in C++03 it will not compile.

> Username: denzor200  
> Created_at: 2022-01-13 03:40:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r783608196  

I see 3 variants to resolve it:  
1. To add a special flag on the `transform_view` side that will allow to ignore `F` when `value_of` or `value_at`.  
2. To patch all Sequence's and Iterator's methods (`value_of`, `value_at`, `begin`, `end`, etc..). Add ability to override implementations selected by `tag_of`.  
3. To implement `identity_view` manually.. Don't use `transform_view` and some identity funobject.

> Username: denzor200  
> Created_at: 2022-01-13 12:57:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r783932814  

4. To implement specific identity funobject(without rvalue) manually. Don't use `std::identity` or `boost::identity`, even after C++11 standards, we don't need it cause of fusion don't use rvalues.

> Username: denzor200  
> Created_at: 2022-01-14 04:46:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r784504498  

> To add a special flag on the `transform_view` side that will allow to ignore `F` when `value_of` or `value_at`.  
  
I suppose that ability to set "specific funobject for **prvalue**" will be better. And then, templated iface of `transform_view` will be like this:  
```  
template <typename Sequence, typename F1, typename FPRV = F1>  
struct transform_view;  
```  
  
And then we can use it on `identity_view` side like this:  
```  
struct prval_identity {  
    template<typename T>  
    T operator() (T val) const;  
};  
  
transform_view<Sequence, boost::identity, prval_identity> tv(seq, boost::identity(), prval_identity());  
```  
  
  
> To patch all Sequence's and Iterator's methods (`value_of`, `value_at`, `begin`, `end`, etc..). Add ability to override implementations selected by `tag_of`.  
  
Too many changes patch.  
  
> To implement `identity_view` manually.. Don't use `transform_view` and some identity funobject.  
  
Guaranteed to work, but we will leave this variant extreme.  
  
> To implement specific identity funobject(without rvalue) manually. Don't use `std::identity` or `boost::identity`, even after C++11 standards, we don't need it cause of fusion don't use rvalues.  
  
It's wrong. We need identity with rvalues support, we can't spoil, for example, this sequence: `vector<std::string, std::string&&>`.  
We really need identity funobject, that won't spoil **prvalues**, but its impossible, for example:  
```  
// It will not spoil prvalues, but it will be compile time error for other value categories:  
// - error: call of '(identity) (int&)' is ambiguous  
// - error: call of '(identity) (const int&)' is ambiguous  
struct identity   
{  
   template<typename T>  
   T operator() (T val) const;  
  
   template<typename T>  
   T& operator() ( T& val) const;  
};  
```

> Username: djowel  
> Created_at: 2022-01-14 05:58:13 UTC  
> Updated_at: 2022-01-14 05:58:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r784524756  

> 4. To implement specific identity funobject(without rvalue) manually. Don't use `std::identity` or `boost::identity`, even after C++11 standards, we don't need it cause of fusion don't use rvalues.  
  
More correctly, C++03 does not have r-values...  
  
Makes me think that we need to decide when to move on and leave C++03. I think the time is now. I think we can do this incrementally. I also think that new features should be free from having to support C++03. It's best to just forget about C++03 for this extension. Test for C++ version in the code and disable entire headers and tests when 03 is detected. Of course, this should be documented as well.

> Username: denzor200  
> Created_at: 2022-01-14 10:29:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r784731399  

@djowel, OK.  
What do you think about this strategy?  
  
> I suppose that ability to set "specific funobject for **prvalue**" will be better. And then, templated iface of `transform_view` will be like this:  
> ```  
> template <typename Sequence, typename F1, typename FPRV = F1>  
> struct transform_view;  
> ```  
>   
> And then we can use it on `identity_view` side like this:  
> ```  
> struct prval_identity {  
>     template<typename T>  
>     T operator() (T val) const;  
> };  
>   
> transform_view<Sequence, boost::identity, prval_identity> tv(seq, boost::identity(), prval_identity());  
> ```  
  
If it unacceptable, i will recreate `identity_view` manually.

> Username: djowel  
> Created_at: 2022-01-14 16:14:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r784972847  

It's looking like the limitations of the implementation is seeping out the API.

> Username: denzor200  
> Created_at: 2022-01-15 12:24:53 UTC  
> Updated_at: 2022-01-15 12:24:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r785303142  

> 4. To implement specific identity funobject(without rvalue) manually. Don't use `std::identity` or `boost::identity`, even after C++11 standards, we don't need it cause of fusion don't use rvalues.  
  
5. Implement identity funobject in a detail namespace, and then add `boost::result_of` specialization for it.  
Just like... https://godbolt.org/z/6Yne5cTb4  
I know it looks like a dirty crutch, but it works great for us.

> Username: djowel  
> Created_at: 2022-01-15 14:40:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r785314177  

> 5\. Implement identity funobject in a detail namespace, and then add `boost::result_of` specialization for it.  
>     Just like... https://godbolt.org/z/6Yne5cTb4  
>     I know it looks like a dirty crutch, but it works great for us.  
  
Go for it

> Username: denzor200  
> Created_at: 2022-01-15 18:29:36 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r785339926  

@djowel docs was not checked. I have no ability to compile them now.

> Username: djowel  
> Created_at: 2022-01-16 00:39:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#discussion_r785376179  

Look at that! Clean build!


---

## Comment 4

> Username: pdimov  
> Created_at: 2022-06-17 20:41:36 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#issuecomment-1159213885  

This introduced a dependency on Functional. Might be better to just copy the implementation of `identity` here.  
  
Failing that, this should be merged to master because the difference in dependencies causes problems for CMake tests in other libraries such as Unordered (because depinst installs Functional as an indirect dependency on develop, but not on master.) CMakeLists.txt should probably be updated with the new dependency, too. (`boostdep --cmake fusion > CMakeLists.txt`.)

---

## Comment 5

> Username: denzor200  
> Created_at: 2022-07-04 10:28:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/240#issuecomment-1173647796  

@pdimov I created two PRs here(https://github.com/boostorg/fusion/pull/250 and https://github.com/boostorg/fusion/pull/251).  
Hope this is what you need.

---
