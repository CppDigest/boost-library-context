# #36 When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly [Closed]

> Username: XiaLiChao82  
> Created at: 2019-12-01 02:43:11 UTC  
> Updated at: 2019-12-18 00:51:50 UTC  
> Closed at: 2019-12-17 14:36:17 UTC  
> Url: https://github.com/boostorg/dll/pull/36  

matching the name obtained by ctti_type_index::type_id.  
    2.Resolved the problem where ctti_type_index::type_id could not be  
    obtained when the template type in demangle contains default  
parameters.  
    3.Resolved the problem of ctti_type_index::type_id getting the  
function  
    name without Spaces between the return type and the parentheses when  
the  
    template parameter type in demangle is a function.  
    4.Resolved the problem of ctti_type_index::type_id in demangle  
getting  
    no Spaces between the end '>' of the two templates

---

## Comment 1

> Username: apolukhin  
> Created_at: 2019-12-03 05:02:36 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-560999781  

Looks good, but please provide tests (looks like you forgot to `git add` one of the files).

---

## Comment 2

> Username: XiaLiChao82  
> Created_at: 2019-12-03 09:27:00 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-561077229  

> Looks good, but please provide tests (looks like you forgot to `git add` one of the files).  
  
Sorry, I added my test case directly to test/cpp_test_library.cpp, causing branch conflicts

---

## Comment 3

> Username: XiaLiChao82  
> Created_at: 2019-12-03 09:29:01 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-561078005  

> > Looks good, but please provide tests (looks like you forgot to `git add` one of the files).  
>   
> Sorry, I added my test case directly to test/cpp_test_library.cpp, causing branch conflicts  
  
Do I need to add a new file? I tested it myself with the boost test

---

## Comment 4

> Username: apolukhin  
> Created_at: 2019-12-03 18:00:19 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-561285053  

> Do I need to add a new file? I tested it myself with the boost test  
  
You can add tests to existing files. Or you may add tests to new one - we do not have strict rules on this topic.  
  
Please resolve the conflicts

---

## Comment 5

> Username: XiaLiChao82  
> Created_at: 2019-12-04 10:52:48 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-561588749  

I have fixed the branch conflict, but the ci test compilation failed  
  
---Original---  
From: "Antony Polukhin"<notifications@github.com&gt;  
Date: Wed, Dec 4, 2019 02:00 AM  
To: "boostorg/dll"<dll@noreply.github.com&gt;;  
Cc: "Author"<author@noreply.github.com&gt;;"XiaLiChao82"<564112343@qq.com&gt;;  
Subject: Re: [boostorg/dll] When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly (#36)  
  
  
   
Do I need to add a new file? I tested it myself with the boost test  
    
You can add tests to existing files. Or you may add tests to new one - we do not have strict rules on this topic.  
   
Please resolve the conflicts  
   
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Review 6 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-12-04 11:32:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dll/pull/36#pullrequestreview-326776833  

Looks very good. Added a few comments, please address those

📁 include/boost/dll/detail/demangling/itanium.hpp

```diff
 111 |+     std::string parse_type(const mangled_storage_impl & ms, dummy<R(Args...)>*);
 112 |+ 
 113 |+     std::string parse_type(const mangled_storage_impl & ms, dummy<>*);
```

> Username: apolukhin  
> Created_at: 2019-12-04 11:25:45 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r353688528  

Make this function `inline` to avoid link errors in CI and user code

---

📁 include/boost/dll/detail/demangling/itanium.hpp

```diff
  96 |+     std::string get_type(const mangled_storage_impl & ms, dummy<T>*);
  97 |+ 
  98 |+     template <typename... T, template <typename...> typename Tn>
```

> Username: apolukhin  
> Created_at: 2019-12-04 11:27:36 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r353689289  

Use `template <typename...> class ` instead of `template <typename...> typename ` to fix the warning https://travis-ci.com/boostorg/dll/jobs/262855135#L1633

---

📁 include/boost/dll/detail/demangling/itanium.hpp

```diff
  99 |+     std::string get_type(const mangled_storage_impl & ms, dummy<Tn<T...>>*);
 100 |+ 
 101 |+     template <typename... T, template <typename...> typename Tn>
```

> Username: apolukhin  
> Created_at: 2019-12-04 11:27:49 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r353689395  

Use `template <typename...> class ` instead of `template <typename...> typename ` to fix the warning https://travis-ci.com/boostorg/dll/jobs/262855135#L1633

---

📁 include/boost/dll/detail/demangling/itanium.hpp

```diff
 179 |+     template <typename T>
 180 |+     std::string get_type(const mangled_storage_impl & ms, dummy<T>*) {
 181 |+         using namespace std;
```

> Username: apolukhin  
> Created_at: 2019-12-04 11:29:06 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r353689837  

Please remove all the `using namespace std;` wherever possible.


📁 test/Jamfile.v2

```diff
  93 |         [ run cpp_import_test.cpp   : : cpp_plugin ]
  94 |         [ run template_method_linux_test.cpp : : cpp_plugin ]
  95 |+         [ run gcc_ctti_type_name_parser_test.cpp : : cpp_mangle_plugin ]
```

> Username: apolukhin  
> Created_at: 2019-12-04 11:31:36 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r353690884  

Please rename the file, don't make it GCC specific. The test would probably work well on clang and icc if it works on GCC.


---

## Comment 7

> Username: XiaLiChao82  
> Created_at: 2019-12-07 15:03:00 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-562858565  

I modified the relevant code according to your suggestion, which seems to have solved most of the CI compilation problems. Besides, I also dealt with the multiple pointer with CV qualifier and the demangle name resolution of function pointer, but the function pointer with CV qualifier has not been solved.  
  
  
  
  
  
  
------------------&nbsp;原始邮件&nbsp;------------------  
发件人:&nbsp;"Antony Polukhin"<notifications@github.com&gt;;  
发送时间:&nbsp;2019年12月4日(星期三) 晚上7:32  
收件人:&nbsp;"boostorg/dll"<dll@noreply.github.com&gt;;  
抄送:&nbsp;"夏立超"<564112343@qq.com&gt;;"Author"<author@noreply.github.com&gt;;  
主题:&nbsp;Re: [boostorg/dll] When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly (#36)  
  
  
  
  
@apolukhin requested changes on this pull request.  
   
Looks very good. Added a few comments, please address those  
  
   
In include/boost/dll/detail/demangling/itanium.hpp:  
 &gt; +    template <typename... T, template <typename...&gt; typename Tn&gt; +    std::string get_type(const mangled_storage_impl &amp; ms, dummy<Tn<T...&gt;&gt;*); + +    template <typename... T, template <typename...&gt; typename Tn&gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<Tn<T...&gt;&gt;*); + +    template <typename T&gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<T&gt;*); + +    template <typename T1, typename T2, typename... T3&gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<T1, T2, T3...&gt;*); + +    template <typename R, typename... Args&gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<R(Args...)&gt;*); + +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<&gt;*);    
Make this function inline to avoid link errors in CI and user code  
   
   
In include/boost/dll/detail/demangling/itanium.hpp:  
 &gt; @@ -88,6 +88,29 @@ class mangled_storage_impl : public mangled_storage_base    namespace parser  { +    //! declare +    template <typename... T&gt; +    struct dummy; + +    template <typename T&gt; +    std::string get_type(const mangled_storage_impl &amp; ms, dummy<T&gt;*); + +    template <typename... T, template <typename...&gt; typename Tn&gt;    
Use template <typename...&gt; class  instead of template <typename...&gt; typename  to fix the warning https://travis-ci.com/boostorg/dll/jobs/262855135#L1633  
   
   
In include/boost/dll/detail/demangling/itanium.hpp:  
 &gt; @@ -88,6 +88,29 @@ class mangled_storage_impl : public mangled_storage_base    namespace parser  { +    //! declare +    template <typename... T&gt; +    struct dummy; + +    template <typename T&gt; +    std::string get_type(const mangled_storage_impl &amp; ms, dummy<T&gt;*); + +    template <typename... T, template <typename...&gt; typename Tn&gt; +    std::string get_type(const mangled_storage_impl &amp; ms, dummy<Tn<T...&gt;&gt;*); + +    template <typename... T, template <typename...&gt; typename Tn&gt;    
Use template <typename...&gt; class  instead of template <typename...&gt; typename  to fix the warning https://travis-ci.com/boostorg/dll/jobs/262855135#L1633  
   
   
In include/boost/dll/detail/demangling/itanium.hpp:  
 &gt;      }        template<typename Return&gt;      std::string arg_list(const mangled_storage_impl &amp;, Return (*)())      {          return "";      } + +    //! implement +    template <typename T&gt; +    std::string get_type(const mangled_storage_impl &amp; ms, dummy<T&gt;*) { +        using namespace std;    
Please remove all the using namespace std; wherever possible.  
   
   
In test/Jamfile.v2:  
 &gt; @@ -91,5 +92,6 @@ project          [ run cpp_load_test.cpp   : : cpp_plugin ]          [ run cpp_import_test.cpp   : : cpp_plugin ]          [ run template_method_linux_test.cpp : : cpp_plugin ] +        [ run gcc_ctti_type_name_parser_test.cpp : : cpp_mangle_plugin ]    
Please rename the file, don't make it GCC specific. The test would probably work well on clang and icc if it works on GCC.  
   
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Review 8 [Changes requested]

> Username: apolukhin  
> Created_at: 2019-12-09 17:22:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/dll/pull/36#pullrequestreview-329083885  

📁 include/boost/dll/detail/demangling/itanium.hpp

```diff
 118 |+ 
 119 |+     template<typename T>
 120 |+     using rm_ref = typename remove_reference<T>::type;
```

> Username: apolukhin  
> Created_at: 2019-12-09 17:17:05 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r355576180  

includes for boost::remove_pointer and boost::remove_reference are missing. Because of that there's an error https://travis-ci.com/boostorg/dll/jobs/264314238#L1138


📁 test/ctti_type_name_parser_test.cpp

```diff
  76 |+ 
  77 |+   auto ctor6 = ms.get_constructor<alias(const volatile std::string*)>();
  78 |+   BOOST_TEST(!ctor6.empty());
```

> Username: apolukhin  
> Created_at: 2019-12-09 17:22:20 UTC  
> Updated_at: 2019-12-17 14:06:30 UTC  
> Url: https://github.com/boostorg/dll/pull/36#discussion_r355578717  

Clang-5 fails some of this tests https://travis-ci.com/boostorg/dll/jobs/264314240#L2101  
Please try fixing those


---

## Comment 9

> Username: XiaLiChao82  
> Created_at: 2019-12-10 05:25:16 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-563840853  

I tried to install clang-5.0 on Ubuntu18.10 and recompile the test case, std::string in the exported demangle is std::__cxx11::basic_string<char, std::char_traits<char&gt;, std::allocator<char&gt; &gt;, but the exported symbol in the CI test case is std::string.  
  
  
  
  
------------------&nbsp;原始邮件&nbsp;------------------  
发件人: "Antony Polukhin"<notifications@github.com&gt;;   
发送时间: 2019年12月10日(星期二) 凌晨1:22  
收件人: "boostorg/dll"<dll@noreply.github.com&gt;;   
抄送: "夏立超"<564112343@qq.com&gt;; "Author"<author@noreply.github.com&gt;;   
主题: Re: [boostorg/dll] When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly (#36)  
  
  
  
  
@apolukhin requested changes on this pull request.  
   
   
In include/boost/dll/detail/demangling/itanium.hpp:  
 &gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<T&gt;*); + +    template <typename T1, typename T2, typename... T3&gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<T1, T2, T3...&gt;*); + +    template <typename R, typename... Args&gt; +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<R(Args...)&gt;*); + +    std::string parse_type(const mangled_storage_impl &amp; ms, dummy<&gt;*); + +    template<typename T&gt; +    std::string +    type_name(const mangled_storage_impl &amp;); + +    template<typename T&gt; +    using rm_ref = typename remove_reference<T&gt;::type;    
includes for boost::remove_pointer and boost::remove_reference are missing. Because of that there's an error https://travis-ci.com/boostorg/dll/jobs/264314238#L1138  
   
   
In test/ctti_type_name_parser_test.cpp:  
 &gt; +  BOOST_TEST(!ctor1.empty()); + +  auto ctor2 = ms.get_constructor<alias(int*)&gt;(); +  BOOST_TEST(!ctor2.empty()); + +  auto ctor3 = ms.get_constructor<alias(const int*)&gt;(); +  BOOST_TEST(!ctor3.empty()); + +  auto ctor4 = ms.get_constructor<alias(const volatile int*)&gt;(); +  BOOST_TEST(!ctor4.empty()); + +  auto ctor5 = ms.get_constructor<alias(const std::string&amp;)&gt;(); +  BOOST_TEST(!ctor5.empty()); + +  auto ctor6 = ms.get_constructor<alias(const volatile std::string*)&gt;(); +  BOOST_TEST(!ctor6.empty());    
Clang-5 fails some of this tests https://travis-ci.com/boostorg/dll/jobs/264314240#L2101  
 Please try fixing those  
   
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Comment 10

> Username: apolukhin  
> Created_at: 2019-12-10 05:43:34 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-563865399  

> I tried to install clang-5.0 on Ubuntu18.10 and recompile the test case, std::string in the exported demangle is std::__cxx11::basic_string<char, std::char_traits<char&gt;, std::allocator<char&gt; &gt;, but the exported symbol in the CI test case is std::string.  
  
Thats's fine. Your clang uses a new version of standard library. If you can reproduce the error, or there's some other error - try fixing it.

---

## Comment 11

> Username: XiaLiChao82  
> Created_at: 2019-12-10 06:28:31 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-563885583  

Okay!  
  
  
  
------------------&nbsp;原始邮件&nbsp;------------------  
发件人: "Antony Polukhin"<notifications@github.com&gt;;   
发送时间: 2019年12月10日(星期二) 中午1:43  
收件人: "boostorg/dll"<dll@noreply.github.com&gt;;   
抄送: "夏立超"<564112343@qq.com&gt;; "Author"<author@noreply.github.com&gt;;   
主题: Re: [boostorg/dll] When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly (#36)  
  
  
  
   
I tried to install clang-5.0 on Ubuntu18.10 and recompile the test case, std::string in the exported demangle is std::__cxx11::basic_string<char, std::char_traits<char&gt;, std::allocator<char&gt; &gt;, but the exported symbol in the CI test case is std::string.  
    
Thats's fine. Your clang uses a new version of standard library. If you can reproduce the error, or there's some other error - try fixing it.  
   
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Comment 12

> Username: XiaLiChao82  
> Created_at: 2019-12-16 01:06:33 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-565868059  

I am sorry that I have had time to deal with this problem recently.In my tests, I found that when I defined _GLIBCXX_USE_CXX11_ABI=0, the mangled symbol of std::string was Ss, and the name of demangled was also std::string,On the other hand, when defining _GLIBCXX_USE_CXX11_ABI=1, the mangled symbol is nst7__cxxxx_stringicst11char_traitsicesaiceee, demangled name is std::__cxx11::basic_string<char, std::char_traits<char&gt;, std::allocator<char&gt; &gt;  
  
  
  
  
------------------&nbsp;原始邮件&nbsp;------------------  
发件人: "Antony Polukhin"<notifications@github.com&gt;;   
发送时间: 2019年12月10日(星期二) 中午1:43  
收件人: "boostorg/dll"<dll@noreply.github.com&gt;;   
抄送: "夏立超"<564112343@qq.com&gt;; "Author"<author@noreply.github.com&gt;;   
主题: Re: [boostorg/dll] When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly (#36)  
  
  
  
   
I tried to install clang-5.0 on Ubuntu18.10 and recompile the test case, std::string in the exported demangle is std::__cxx11::basic_string<char, std::char_traits<char&gt;, std::allocator<char&gt; &gt;, but the exported symbol in the CI test case is std::string.  
    
Thats's fine. Your clang uses a new version of standard library. If you can reproduce the error, or there's some other error - try fixing it.  
   
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---

## Comment 13

> Username: XiaLiChao82  
> Created_at: 2019-12-18 00:51:49 UTC  
> Url: https://github.com/boostorg/dll/pull/36#issuecomment-566816886  

I tried to fix a bug using a lower version of the ABI, but failed  
  
  
---Original---  
From: "Antony Polukhin"<notifications@github.com&gt;  
Date: Tue, Dec 10, 2019 13:43 PM  
To: "boostorg/dll"<dll@noreply.github.com&gt;;  
Cc: "Author"<author@noreply.github.com&gt;;"XiaLiChao82"<564112343@qq.com&gt;;  
Subject: Re: [boostorg/dll] When rtti is closed in GCC, the pointer type with the CV qualifier and the template type with the default parameters are resolved correctly (#36)  
  
  
   
I tried to install clang-5.0 on Ubuntu18.10 and recompile the test case, std::string in the exported demangle is std::__cxx11::basic_string<char, std::char_traits<char&gt;, std::allocator<char&gt; &gt;, but the exported symbol in the CI test case is std::string.  
    
Thats's fine. Your clang uses a new version of standard library. If you can reproduce the error, or there's some other error - try fixing it.  
   
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or unsubscribe.

---
