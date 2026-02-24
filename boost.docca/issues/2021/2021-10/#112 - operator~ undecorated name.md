# #112 - operator~ undecorated name [Closed]

> Username: vinniefalco  
> Created at: 2021-10-31 20:50:19 UTC  
> Updated at: 2021-11-03 18:49:48 UTC  
> Closed at: 2021-11-03 18:49:48 UTC  
> Url: https://github.com/boostorg/docca/issues/112  

When `operator~` is converted to a ref it comes out as `operator_` which can conflict. We might want something like `operator_tilde_` or `operator_bnot_` (for bitwise not). Or something. For example this is the URL `html/url/ref/boost__urls__bnf__lut_chars/operator_.html` and this is the code:  
  
```  
    constexpr  
    lut_chars  
    operator~() const noexcept  
```

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-11-01 05:17:42 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-955941039  

Here's my proposed change for this one:  
  
```diff  
diff --git a/include/docca/common.xsl b/include/docca/common.xsl  
index b9b02c5..771cc32 100644  
--- a/include/docca/common.xsl  
+++ b/include/docca/common.xsl  
@@ -60,8 +60,7 @@  
     <replace pattern="->" with="_arrow_"/>  
     <replace pattern="&lt;" with="_lt_"/>  
     <replace pattern=">"    with="_gt_"/>  
-    <replace pattern="^~" with="_dtor_"/>  <!-- destructor -->  
-    <replace pattern="~" with="_"/>  
+    <replace pattern="~" with="_tilde_"/>  
     <replace pattern="\[" with="_lb_"/>  
     <replace pattern="\]" with="_rb_"/>  
     <replace pattern="\(" with="_lp_"/>  
```  
  
The `dtor` (destructor) phrase usually doesn't take effect as intended, because although the tilde comes at the beginning of the function name itself, it doesn't come at the beginning of the whole string (which includes the namespace, etc.). So tilde is always getting replaced with just `_` anyway. We could try to make a more sophisticated regular expression to successfully identify the destructors, but since I couldn't find any instances of these in any of our existing docca projects, there's less pressure to preserve any existing behavior. For that reason, I suggest just always replacing `~` with `_tilde_`, which is akin to most of our replacements (describing the character rather than its semantics). This would ensure that it doesn't collide with names like `operator_` or `_authority_view`.  
  
@vinniefalco What say you on this one? Shall I go with the proposed change? Example impacts are shown below.  
  
Here are the effects of the above change in the url library:  
  
```diff  
diff -r htmlPREVIOUS_SCRUBBED/url_HTML.manifest html_SCRUBBED/url_HTML.manifest  
39c39  
< url/ref/boost__urls__authority_view/_authority_view.html  
---  
> url/ref/boost__urls__authority_view/_tilde_authority_view.html  
75c75  
< url/ref/boost__urls__basic_static_pool/_basic_static_pool.html  
---  
> url/ref/boost__urls__basic_static_pool/_tilde_basic_static_pool.html  
162c162  
< url/ref/boost__urls__bnf__lut_chars/operator_.html  
---  
> url/ref/boost__urls__bnf__lut_chars/operator_tilde_.html  
589c589  
< url/ref/boost__urls__static_url/_static_url.html  
---  
> url/ref/boost__urls__static_url/_tilde_static_url.html  
704c704  
< url/ref/boost__urls__string_value/_string_value.html  
---  
> url/ref/boost__urls__string_value/_tilde_string_value.html  
729c729  
< url/ref/boost__urls__url/_url.html  
---  
> url/ref/boost__urls__url/_tilde_url.html  
857c857  
< url/ref/boost__urls__url_view/_url_view.html  
---  
> url/ref/boost__urls__url_view/_tilde_url_view.html  
921c921  
< url/ref/boost__urls__params_view__value_type/_value_type.html  
---  
> url/ref/boost__urls__params_view__value_type/_tilde_value_type.html  
```  
  
And the json library:  
  
```diff  
diff -r htmlPREVIOUS_SCRUBBED/json_HTML.manifest html_SCRUBBED/json_HTML.manifest  
42c42  
< json/ref/boost__json__array/_array.html  
---  
> json/ref/boost__json__array/_tilde_array.html  
126c126  
< json/ref/boost__json__basic_parser/_basic_parser.html  
---  
> json/ref/boost__json__basic_parser/_tilde_basic_parser.html  
144c144  
< json/ref/boost__json__key_value_pair/_key_value_pair.html  
---  
> json/ref/boost__json__key_value_pair/_tilde_key_value_pair.html  
169c169  
< json/ref/boost__json__monotonic_resource/_monotonic_resource.html  
---  
> json/ref/boost__json__monotonic_resource/_tilde_monotonic_resource.html  
191c191  
< json/ref/boost__json__object/_object.html  
---  
> json/ref/boost__json__object/_tilde_object.html  
270c270  
< json/ref/boost__json__parser/_parser.html  
---  
> json/ref/boost__json__parser/_tilde_parser.html  
298c298  
< json/ref/boost__json__serializer/_serializer.html  
---  
> json/ref/boost__json__serializer/_tilde_serializer.html  
311c311  
< json/ref/boost__json__static_resource/_static_resource.html  
---  
> json/ref/boost__json__static_resource/_tilde_static_resource.html  
314c314  
< json/ref/boost__json__storage_ptr/_storage_ptr.html  
---  
> json/ref/boost__json__storage_ptr/_tilde_storage_ptr.html  
342c342  
< json/ref/boost__json__stream_parser/_stream_parser.html  
---  
> json/ref/boost__json__stream_parser/_tilde_stream_parser.html  
376c376  
< json/ref/boost__json__string/_string.html  
---  
> json/ref/boost__json__string/_tilde_string.html  
525c525  
< json/ref/boost__json__value/_value.html  
---  
> json/ref/boost__json__value/_tilde_value.html  
685c685  
< json/ref/boost__json__value_stack/_value_stack.html  
---  
> json/ref/boost__json__value_stack/_tilde_value_stack.html  
```  
  
And the Beast library:  
  
```diff  
diff -r htmlPREVIOUS_SCRUBBED/beast_HTML.manifest html_SCRUBBED/beast_HTML.manifest  
18c18  
< beast/ref/boost__beast__async_base/_async_base.html  
---  
> beast/ref/boost__beast__async_base/_tilde_async_base.html  
55c55  
< beast/ref/boost__beast__http__basic_fields/_basic_fields.html  
---  
> beast/ref/boost__beast__http__basic_fields/_tilde_basic_fields.html  
127c127  
< beast/ref/boost__beast__basic_flat_buffer/_basic_flat_buffer.html  
---  
> beast/ref/boost__beast__basic_flat_buffer/_tilde_basic_flat_buffer.html  
164c164  
< beast/ref/boost__beast__basic_multi_buffer/_basic_multi_buffer.html  
---  
> beast/ref/boost__beast__basic_multi_buffer/_tilde_basic_multi_buffer.html  
214c214  
< beast/ref/boost__beast__http__basic_parser/_basic_parser.html  
---  
> beast/ref/boost__beast__http__basic_parser/_tilde_basic_parser.html  
237c237  
< beast/ref/boost__beast__test__basic_stream/_basic_stream.html  
---  
> beast/ref/boost__beast__test__basic_stream/_tilde_basic_stream.html  
275c275  
< beast/ref/boost__beast__basic_stream/_basic_stream.html  
---  
> beast/ref/boost__beast__basic_stream/_tilde_basic_stream.html  
507c507  
< beast/ref/boost__beast__file_posix/_file_posix.html  
---  
> beast/ref/boost__beast__file_posix/_tilde_file_posix.html  
525c525  
< beast/ref/boost__beast__file_stdio/_file_stdio.html  
---  
> beast/ref/boost__beast__file_stdio/_tilde_file_stdio.html  
543c543  
< beast/ref/boost__beast__file_win32/_file_win32.html  
---  
> beast/ref/boost__beast__file_win32/_tilde_file_win32.html  
609c609  
< beast/ref/boost__beast__flat_stream/_flat_stream.html  
---  
> beast/ref/boost__beast__flat_stream/_tilde_flat_stream.html  
628c628  
< beast/ref/boost__beast__test__handler/_handler.html  
---  
> beast/ref/boost__beast__test__handler/_tilde_handler.html  
674c674  
< beast/ref/boost__beast__http__icy_stream/_icy_stream.html  
---  
> beast/ref/boost__beast__http__icy_stream/_tilde_icy_stream.html  
775c775  
< beast/ref/boost__beast__http__parser/_parser.html  
---  
> beast/ref/boost__beast__http__parser/_tilde_parser.html  
851c851  
< beast/ref/boost__beast__unit_test__runner/_runner.html  
---  
> beast/ref/boost__beast__unit_test__runner/_tilde_runner.html  
877c877  
< beast/ref/boost__beast__saved_handler/_saved_handler.html  
---  
> beast/ref/boost__beast__saved_handler/_tilde_saved_handler.html  
887c887  
< beast/ref/boost__beast__unit_test__suite__scoped_testcase/_scoped_testcase.html  
---  
> beast/ref/boost__beast__unit_test__suite__scoped_testcase/_tilde_scoped_testcase.html  
992c992  
< beast/ref/boost__beast__stable_async_base/_stable_async_base.html  
---  
> beast/ref/boost__beast__stable_async_base/_tilde_stable_async_base.html  
1182c1182  
< beast/ref/boost__beast__websocket__stream/_stream.html  
---  
> beast/ref/boost__beast__websocket__stream/_tilde_stream.html  
1296c1296  
< beast/ref/boost__beast__unit_test__suite/_suite.html  
---  
> beast/ref/boost__beast__unit_test__suite/_tilde_suite.html  
1361c1361  
< beast/ref/boost__beast__http__basic_file_body__value_type/_value_type.html  
---  
> beast/ref/boost__beast__http__basic_file_body__value_type/_tilde_value_type.html  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-11-02 01:16:14 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-957014794  

oof.. so this is going to change all the links for destructors?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-11-02 01:16:43 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-957014991  

I wish destructor symbols could read `_dtor_saved_handler` for example, is that possible?

---

## Comment 4

> Username: evanlenz  
> Created at: 2021-11-02 05:33:22 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-957114720  

Okay, here's an alternative code change which appears to successfully do what was originally intended with `dtor`:  
  
```diff  
diff --git a/include/docca/common.xsl b/include/docca/common.xsl  
index b9b02c5..da9b741 100644  
--- a/include/docca/common.xsl  
+++ b/include/docca/common.xsl  
@@ -60,8 +60,8 @@  
     <replace pattern="->"   with="_arrow_"/>  
     <replace pattern="&lt;" with="_lt_"/>  
     <replace pattern=">"    with="_gt_"/>  
-    <replace pattern="^~" with="_dtor_"/>  <!-- destructor -->  
-    <replace pattern="~" with="_"/>  
+    <replace pattern="\.~"  with="._dtor_"/>  
+    <replace pattern="~"    with="_tilde_"/>  
     <replace pattern="\["   with="_lb_"/>  
     <replace pattern="\]"   with="_rb_"/>  
     <replace pattern="\("   with="_lp_"/>  
```  
  
Impacts on URL library:  
  
```diff  
diff -r htmlPREVIOUS_SCRUBBED/url_HTML.manifest html_SCRUBBED/url_HTML.manifest  
39c39  
< url/ref/boost__urls__authority_view/_authority_view.html  
---  
> url/ref/boost__urls__authority_view/_dtor_authority_view.html  
75c75  
< url/ref/boost__urls__basic_static_pool/_basic_static_pool.html  
---  
> url/ref/boost__urls__basic_static_pool/_dtor_basic_static_pool.html  
162c162  
< url/ref/boost__urls__bnf__lut_chars/operator_.html  
---  
> url/ref/boost__urls__bnf__lut_chars/operator_tilde_.html  
589c589  
< url/ref/boost__urls__static_url/_static_url.html  
---  
> url/ref/boost__urls__static_url/_dtor_static_url.html  
704c704  
< url/ref/boost__urls__string_value/_string_value.html  
---  
> url/ref/boost__urls__string_value/_dtor_string_value.html  
729c729  
< url/ref/boost__urls__url/_url.html  
---  
> url/ref/boost__urls__url/_dtor_url.html  
857c857  
< url/ref/boost__urls__url_view/_url_view.html  
---  
> url/ref/boost__urls__url_view/_dtor_url_view.html  
921c921  
< url/ref/boost__urls__params_view__value_type/_value_type.html  
---  
> url/ref/boost__urls__params_view__value_type/_dtor_value_type.html  
```  
  
Impacts on JSON library:  
  
```diff  
diff -r htmlPREVIOUS_SCRUBBED/json_HTML.manifest html_SCRUBBED/json_HTML.manifest  
42c42  
< json/ref/boost__json__array/_array.html  
---  
> json/ref/boost__json__array/_dtor_array.html  
126c126  
< json/ref/boost__json__basic_parser/_basic_parser.html  
---  
> json/ref/boost__json__basic_parser/_dtor_basic_parser.html  
144c144  
< json/ref/boost__json__key_value_pair/_key_value_pair.html  
---  
> json/ref/boost__json__key_value_pair/_dtor_key_value_pair.html  
169c169  
< json/ref/boost__json__monotonic_resource/_monotonic_resource.html  
---  
> json/ref/boost__json__monotonic_resource/_dtor_monotonic_resource.html  
191c191  
< json/ref/boost__json__object/_object.html  
---  
> json/ref/boost__json__object/_dtor_object.html  
270c270  
< json/ref/boost__json__parser/_parser.html  
---  
> json/ref/boost__json__parser/_dtor_parser.html  
298c298  
< json/ref/boost__json__serializer/_serializer.html  
---  
> json/ref/boost__json__serializer/_dtor_serializer.html  
311c311  
< json/ref/boost__json__static_resource/_static_resource.html  
---  
> json/ref/boost__json__static_resource/_dtor_static_resource.html  
314c314  
< json/ref/boost__json__storage_ptr/_storage_ptr.html  
---  
> json/ref/boost__json__storage_ptr/_dtor_storage_ptr.html  
342c342  
< json/ref/boost__json__stream_parser/_stream_parser.html  
---  
> json/ref/boost__json__stream_parser/_dtor_stream_parser.html  
376c376  
< json/ref/boost__json__string/_string.html  
---  
> json/ref/boost__json__string/_dtor_string.html  
525c525  
< json/ref/boost__json__value/_value.html  
---  
> json/ref/boost__json__value/_dtor_value.html  
685c685  
< json/ref/boost__json__value_stack/_value_stack.html  
---  
> json/ref/boost__json__value_stack/_dtor_value_stack.html  
```  
  
Impacts on Beast library:  
  
```diff  
diff -r htmlPREVIOUS_SCRUBBED/beast_HTML.manifest html_SCRUBBED/beast_HTML.manifest  
18c18  
< beast/ref/boost__beast__async_base/_async_base.html  
---  
> beast/ref/boost__beast__async_base/_dtor_async_base.html  
55c55  
< beast/ref/boost__beast__http__basic_fields/_basic_fields.html  
---  
> beast/ref/boost__beast__http__basic_fields/_dtor_basic_fields.html  
127c127  
< beast/ref/boost__beast__basic_flat_buffer/_basic_flat_buffer.html  
---  
> beast/ref/boost__beast__basic_flat_buffer/_dtor_basic_flat_buffer.html  
164c164  
< beast/ref/boost__beast__basic_multi_buffer/_basic_multi_buffer.html  
---  
> beast/ref/boost__beast__basic_multi_buffer/_dtor_basic_multi_buffer.html  
214c214  
< beast/ref/boost__beast__http__basic_parser/_basic_parser.html  
---  
> beast/ref/boost__beast__http__basic_parser/_dtor_basic_parser.html  
237c237  
< beast/ref/boost__beast__test__basic_stream/_basic_stream.html  
---  
> beast/ref/boost__beast__test__basic_stream/_dtor_basic_stream.html  
275c275  
< beast/ref/boost__beast__basic_stream/_basic_stream.html  
---  
> beast/ref/boost__beast__basic_stream/_dtor_basic_stream.html  
507c507  
< beast/ref/boost__beast__file_posix/_file_posix.html  
---  
> beast/ref/boost__beast__file_posix/_dtor_file_posix.html  
525c525  
< beast/ref/boost__beast__file_stdio/_file_stdio.html  
---  
> beast/ref/boost__beast__file_stdio/_dtor_file_stdio.html  
543c543  
< beast/ref/boost__beast__file_win32/_file_win32.html  
---  
> beast/ref/boost__beast__file_win32/_dtor_file_win32.html  
609c609  
< beast/ref/boost__beast__flat_stream/_flat_stream.html  
---  
> beast/ref/boost__beast__flat_stream/_dtor_flat_stream.html  
628c628  
< beast/ref/boost__beast__test__handler/_handler.html  
---  
> beast/ref/boost__beast__test__handler/_dtor_handler.html  
674c674  
< beast/ref/boost__beast__http__icy_stream/_icy_stream.html  
---  
> beast/ref/boost__beast__http__icy_stream/_dtor_icy_stream.html  
775c775  
< beast/ref/boost__beast__http__parser/_parser.html  
---  
> beast/ref/boost__beast__http__parser/_dtor_parser.html  
851c851  
< beast/ref/boost__beast__unit_test__runner/_runner.html  
---  
> beast/ref/boost__beast__unit_test__runner/_dtor_runner.html  
877c877  
< beast/ref/boost__beast__saved_handler/_saved_handler.html  
---  
> beast/ref/boost__beast__saved_handler/_dtor_saved_handler.html  
887c887  
< beast/ref/boost__beast__unit_test__suite__scoped_testcase/_scoped_testcase.html  
---  
> beast/ref/boost__beast__unit_test__suite__scoped_testcase/_dtor_scoped_testcase.html  
992c992  
< beast/ref/boost__beast__stable_async_base/_stable_async_base.html  
---  
> beast/ref/boost__beast__stable_async_base/_dtor_stable_async_base.html  
1182c1182  
< beast/ref/boost__beast__websocket__stream/_stream.html  
---  
> beast/ref/boost__beast__websocket__stream/_dtor_stream.html  
1296c1296  
< beast/ref/boost__beast__unit_test__suite/_suite.html  
---  
> beast/ref/boost__beast__unit_test__suite/_dtor_suite.html  
1361c1361  
< beast/ref/boost__beast__http__basic_file_body__value_type/_value_type.html  
---  
> beast/ref/boost__beast__http__basic_file_body__value_type/_dtor_value_type.html  
```

---

## Comment 5

> Username: evanlenz  
> Created at: 2021-11-02 22:02:12 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958230059  

Anyway, this alternative will also change all those URLs, but now to what you wanted (and what was originally intended). I'll put in the pull request unless you have further concerns.

---

## Comment 6

> Username: evanlenz  
> Created at: 2021-11-02 22:12:13 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958251637  

My concern is that it could break people's links... 😬

---

## Comment 7

> Username: evanlenz  
> Created at: 2021-11-02 22:13:01 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958253399  

Maybe it's not heavily used enough to worry too much about it. I don't know how the docs are released. If they're discrete releases, maybe it's not an issue.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-11-02 22:19:37 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958267624  

What are we calling `operator~` in terms of filename?

---

## Comment 9

> Username: evanlenz  
> Created at: 2021-11-02 22:25:36 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958280369  

operator_tilde_.html  
  
The new (pending) replacement rules are as follows:  
  
1. Replace `.~` with `._dtor_`  
2. Then (otherwise), replace `~` with `_tilde_`  
  
Does that sound like the right behavior to you?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2021-11-02 22:43:53 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958316579  

"tilde" sounds non-sterile . It is in fact a bitwise NOT operator. I believe we emit `operator!` as `__not__`? Maybe we can use `__bnot__` for tilde (i.e. a contraction of bitwise not).

---

## Comment 11

> Username: evanlenz  
> Created at: 2021-11-03 02:23:01 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958603867  

Yeah, I can do that. Do you think those two (`_dtor_` and `_bnot_`) cover the possible semantics of that character in names? In any case, I'll run a full comparison against the existing libraries that use docca to see where it shows up, i.e. where `~` appears when not immediately preceded by `.`. (And you're right that `!` gets replaced with `_not_`.)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-11-03 02:27:34 UTC  
> Url: https://github.com/boostorg/docca/issues/112#issuecomment-958605204  

As far as I know in C++ the tilde is only used for destructors `~T()` and for the bitwise-not operator `operator~` as in... `~t` which means flip all the bits.
