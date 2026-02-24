# #88 add boost_system.natvis and interface source files [Merged]

> Username: vinniefalco  
> Created at: 2022-08-19 01:06:14 UTC  
> Updated at: 2022-08-21 22:09:21 UTC  
> Merged at: 2022-08-21 22:01:14 UTC  
> Closed at: 2022-08-21 22:01:14 UTC  
> Url: https://github.com/boostorg/system/pull/88  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-08-19 15:16:43 UTC  
> Url: https://github.com/boostorg/system/pull/88#issuecomment-1220796780  

See: https://github.com/boostorg/core/pull/121#issuecomment-1220796500

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2022-08-19 15:50:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/88#pullrequestreview-1079032513  

📁 include/boost/system/boost_system.natvis

```diff
  14 |+         <DisplayString Condition="id_ == 0xb2ab117a257edfd1">[system_category]</DisplayString>
  15 |+         <DisplayString Condition="id_ == 0xb2ab117a257edfd2">[interop_category]</DisplayString>
  16 |+         <DisplayString>error_category@{(size_t)this}</DisplayString>
```

> Username: pdimov  
> Created_at: 2022-08-19 15:50:14 UTC  
> Url: https://github.com/boostorg/system/pull/88#discussion_r950337957  

There shouldn't be a `size_t` cast here. The identifier of a category with `id_ == 0` is the address of `this`.  
  
And of course when `id_ != 0`, the category identifier should be displayed, not the address.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2022-08-19 15:51:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/88#pullrequestreview-1079033722  

📁 include/boost/system/boost_system.natvis

```diff
   2 |+ <AutoVisualizer xmlns="http://schemas.microsoft.com/vstudio/debugger/natvis/2010">
   3 |+ 
   4 |+     <Type Name="boost::source_location">
```

> Username: pdimov  
> Created_at: 2022-08-19 15:51:10 UTC  
> Updated_at: 2022-08-19 15:51:11 UTC  
> Url: https://github.com/boostorg/system/pull/88#discussion_r950339048  

This should probably go into Boost.Assert as the type is defined there.

> Username: vinniefalco  
> Created_at: 2022-08-19 16:04:29 UTC  
> Updated_at: 2022-08-19 16:04:30 UTC  
> Url: https://github.com/boostorg/system/pull/88#discussion_r950353913  

Well, I figured I would avoid creating one more INTERFACE project with sources added, but yes it can go there.

> Username: vinniefalco  
> Created_at: 2022-08-19 18:28:50 UTC  
> Url: https://github.com/boostorg/system/pull/88#discussion_r950465212  

I moved it to Boost.Assert


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2022-08-19 15:52:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/88#pullrequestreview-1079035251  

📁 include/boost/system/boost_system.natvis

```diff
   5 |+         <DisplayString>{function_,sb} @ {file_,sb}:{line_}</DisplayString>
   6 |+         <Expand>
   7 |+             <Item Name="file">file_,sb</Item>
```

> Username: pdimov  
> Created_at: 2022-08-19 15:52:17 UTC  
> Url: https://github.com/boostorg/system/pull/88#discussion_r950340087  

`line_` should be here too? And `column_`.


---

## Review 5 [Commented]

> Username: pdimov  
> Created_at: 2022-08-19 15:54:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/88#pullrequestreview-1079037726  

📁 include/boost/system/boost_system.natvis

```diff
  33 |+ 
  34 |+     <Type Name="boost::system::result&lt;*,*&gt;">
  35 |+         <DisplayString Condition="v_.ix_ == 1">{v_.st_.rest_.first_}</DisplayString>
```

> Username: pdimov  
> Created_at: 2022-08-19 15:54:12 UTC  
> Url: https://github.com/boostorg/system/pull/88#discussion_r950342432  

Shouldn't this just display `v_`, reusing the variant visualizer?


---

## Comment 6

> Username: pdimov  
> Created_at: 2022-08-21 22:09:20 UTC  
> Url: https://github.com/boostorg/system/pull/88#issuecomment-1221629532  

Huh? How did you manage to merge this PR?

---
