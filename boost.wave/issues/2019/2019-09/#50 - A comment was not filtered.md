# #50 - A comment was not filtered. [Closed]

> Username: wuye9036  
> Created at: 2019-09-05 04:28:29 UTC  
> Updated at: 2020-05-12 15:30:35 UTC  
> Closed at: 2020-05-12 15:30:35 UTC  
> Url: https://github.com/boostorg/wave/issues/50  

```C++  
//  
// Some words.  
  
// /* Some words.  
/* ///// Some words */  
  
/* Some words  
*/////////  
  
/* Some words  
////// *////////  
  
int foo(int a){  
	return a;  
}  
```  
  
It returns tokens  
  
```C++  
/* ///// Some words */  
int foo(int a){  
	return a;  
}  
```  
  
With configuration:  
```C++  
	size_t lang_flag = boost::wave::support_cpp;  
	lang_flag &= ~(boost::wave::support_option_emit_line_directives );  
	lang_flag &= ~(boost::wave::support_option_single_line );  
	lang_flag &= ~(boost::wave::support_option_emit_pragma_directives );  
	wctxt_wrapper->get_wctxt()->set_language( static_cast<boost::wave::language_support>( lang_flag ) );  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-01-10 22:30:46 UTC  
> Url: https://github.com/boostorg/wave/issues/50#issuecomment-573231385  

@wuye9036 thanks for the report; I will look into it. Also can you tell me if this old bug is still valid: https://svn.boost.org/trac10/ticket/6759 ? Thank you.

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-01-11 02:58:00 UTC  
> Updated at: 2020-01-11 02:59:23 UTC  
> Url: https://github.com/boostorg/wave/issues/50#issuecomment-573273573  

I am unable to reproduce this with 1.72. `preserve_comments` seems to properly exclude all comment tokens with the supplied input text:  
```  
postprocessed tokens:  
334 |int| 392 | | 380 |foo| 277 |(| 334 |int| 392 | | 380 |a| 294 |)| 274 |{| 394 |  
| 345 |return| 392 | | 380 |a| 297 |;| 394 |  
| 293 |}| 394 |  
| 401 ||  
```  
code attached.  
[gh50.cpp](https://github.com/boostorg/wave/files/4048898/gh50.cpp.txt)

---

## Comment 3

> Username: jefftrull  
> Created at: 2020-04-28 15:02:52 UTC  
> Url: https://github.com/boostorg/wave/issues/50#issuecomment-620663142  

@wuye9036 If you have an example demonstrating a failure please attach it to this bug. Otherwise I think we should probably close it. Thank you.

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-05-12 15:30:35 UTC  
> Url: https://github.com/boostorg/wave/issues/50#issuecomment-627418628  

Closing due to lack of response from reporter.
