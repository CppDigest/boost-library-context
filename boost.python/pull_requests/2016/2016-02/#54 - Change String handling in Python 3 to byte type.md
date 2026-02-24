# #54 Change String handling in Python 3 to byte type [Open]

> Username: centerionware  
> Created at: 2016-02-04 05:19:59 UTC  
> Updated at: 2020-11-14 02:30:49 UTC  
> Url: https://github.com/boostorg/python/pull/54  

The byte type allows raw binary data to be passed in from std::strings.  
It becomes necessary in the python to .decode('utf-8') in most cases,  
but allows the programmer to make the choice as to when and where the  
text is encoded in python instead of it being assumed it always need be.  
Use case - passing binary in a std::string containing 0xff and others  
to python, passing byte object from python to c++ as an std::string.

---

## Comment 1

> Username: centerionware  
> Created_at: 2016-03-03 07:58:30 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-191642317  

My understanding is in Python 2 std::string -> Python string handles with this behavior. wstring seems more appropriate for a Python 3 c++ string -> Python unicode string than c++ std::string does.  
I fully understand this would have major implications for a large amount of boost::python projects already using python 3.

---

## Comment 2

> Username: wythend  
> Created_at: 2016-12-05 15:47:10 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-264888886  

I have a  c++ char* variable which is gbk encoded. It will have the same issue when c++ char* return to python3 unicode. I want to change the type char* to python3 bytes. how can I do?

---

## Comment 3

> Username: tadeu  
> Created_at: 2017-01-03 13:02:36 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270109609  

I don't agree with this PR. `std::string` is the _de facto_ text type in C++, and `str` is the text type in Python 3 (not `bytes`).  
  
It seems best to assume that `std::string` is UTF-8 encoded everywhere, the conversions should be handled by Boost.Python code, not by the user. If the user is using another encoding, then the user may create wrappers that do the conversion to UTF-8 in *bindings* code, not on Python code that uses the bindings.  
  
See this comment: https://github.com/boostorg/python/issues/85#issuecomment-270108238

---

## Comment 4

> Username: tadeu  
> Created_at: 2017-01-03 13:06:49 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270110349  

If the user is using another encoding _everywhere_, then another option would be to allow the "default encoding" to be UTF-8, but customizable (somethink like calling `boost::python::set_default_string_encoding("gbk")` and `boost::python::set_default_char_pointer_encoding("gbk")`).

---

## Comment 5

> Username: nicoddemus  
> Created_at: 2017-01-03 13:16:28 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270111931  

We have been using `std::string` as UTF-8 encoded text on our code base for some time now, even on Python 2. We have patched `boost::python` internally to automatically decode `std::string` using `UTF-8` so Python sees a `unicode` string (in Python 2) instead of `bytes/str`. This was vital to be able to internationalize our applications.  
  
But I can see why some people might adopt a different convention, for example to automatically convert `std::string` to Python `bytes` in raw form. The disadvantage of this method is that the weight of performing the conversion lies on the Python code, which on Python 3 will not happen implicitly and will have to be done in every call which returns a `std::string`.  
  
Would be possible to add some support to Boost::Python so this can be overwritten by library authors? AFAIU boost's internals that would not be possible right, because boost's conversion registry is global. This is a problem if, for example, I'm using two libraries (`libA` and `libB`) wrapped with Boost::Python. What if `libA` assumes `std::string` will be `UTF-8` encoded and encodes them accordingly, while `libB` does the same but using `latin-1` or `UTF-16`?

---

## Comment 6

> Username: centerionware  
> Created_at: 2017-01-03 23:25:29 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270254749  

The C++ standard only defines std::string to be a string of char's and says nothing about encodings.  It can hold any arbitrary information, not just text in utf-8.  
Putting a single utf-8 char that uses more than one byte can even break things in cpp, ex string.size will not return 1 even though it is only 1 character. I can see string.replace breaking too.  
Try to pass utf-8 in an std::string to microsofts odbc api ( or most of any ms api for that matter ) and things go bad quick too.  
My point is assuming an std::string should always be some (any) encoding is not only not part of the standards, but also not how a lot of libraries work - even gtk/glib implimented their own ustring class for utf8. Wstring on the other hand was designed to approach this problem, although compilers do it differently (16 bits on vc by default and 32 in gcc) which makes it difficult.   
Hence this patch, which allows the devs to decide when and where to decode a string into what encoding and nothing be assumed about the type of data we are trying to pass. It puts a burdon on devs to .decode ('utf-8') in python, which for new projects wouldn't be a huge problem, but as i said before would easily break old large projects. It would be trivial to make a patch for them to revert to this old behavior though, it is a single line of code after all. Maybe an #ifdef legacy_strings ...

---

## Comment 7

> Username: stefanseefeld  
> Created_at: 2017-01-03 23:27:27 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270255031  

I agree, `std::string` should only ever hold ASCII content, never Unicode.

---

## Comment 8

> Username: centerionware  
> Created_at: 2017-01-04 00:04:29 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270261004  

I am not positive, but i do believe the converters are part of the header portion of boost python(not the compiled library portion). It has been so long i cant remember for sure anymore.

---

## Comment 9

> Username: nicoddemus  
> Created_at: 2017-01-04 01:35:26 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270274198  

> The C++ standard only defines std::string to be a string of char's and says nothing about encodings. It can hold any arbitrary information, not just text in utf-8.  
Putting a single utf-8 char that uses more than one byte can even break things in cpp, ex string.size will not return 1 even though it is only 1 character. I can see string.replace breaking too.  
Try to pass utf-8 in an std::string to microsofts odbc api ( or most of any ms api for that matter ) and things go bad quick too.  
  
Thanks for the response @centerionware, those are all valid points.   
  
We chose to use `std::string` to hold *text* encoded in `UTF-8` for our internal APIs because that seemed the simpler approach given that AFAIK that's what Linux based systems assume when dealing with `char*` variables which are supposed to be text, which is smart given that it is backward-compatible with ASCII and can hold any Unicode character.  
  
But I agree that is not general solution, and can break other APIs which don't follow that assumption. I've seem APIs which return text encoded on the system encoding, which can definitely be incompatible with `UTF-8` (`latin-1` is a common example of this in Latin America and Eastern Europe).  
  
I think returning raw `bytes` like you propose is the *safest* approach, but certainly not the most convenient for Python users given that any functions that receive/return `std::string` will receive/return `bytes` on Python, and users will certainly be surpriesed when they have to write:  
  
```python  
if api.get_name() == b'W1':  
```  
  
Instead of:  
  
```python  
if api.get_name() == 'W1':  
```  
  
As there is no implicit conversion between `bytes` and `str` on Python 3 (which is great if you have to actually deal with international characters).  
  
> I am not positive, but i do believe the converters are part of the header portion of boost python(not the compiled library portion). It has been so long i cant remember for sure anymore.  
  
If that's the case, a library could be compiled with a proper `#define` and it would be great. Even better if that did not depend on a compilation flag but could be declared in the code somehow.

---

## Comment 10

> Username: nicoddemus  
> Created_at: 2017-01-04 01:52:47 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270276446  

@stefanseefeld   
  
> I agree, std::string should only ever hold ASCII content, never Unicode.  
  
I don't agree, `std::string` can hold arbitrary data not only `ASCII` or any other encoded format. That's the original intent of @centerionware's patch, to return binary data from C++ to Python using `std::string`.

---

## Comment 11

> Username: stefanseefeld  
> Created_at: 2017-01-04 02:04:23 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270277903  

My point isn't about this patch, it is about the intent of `std::string`. Its API clearly suggests a fixed-width character type, a requirement which `UTF-8` doesn't meet.  
Likewise, `std::wstring` assumes a fixed-width character-type, so it could hold `UTF-32`, but neither `UTF-8` nor `UTF-16`. Otherwise many of the invariants guaranteed by its API will break.

---

## Comment 12

> Username: tadeu  
> Created_at: 2017-01-04 12:01:51 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270356042  

@stefanseefeld I agree that `size()` and iterator incrementation/decrementation are "broken" for `std::string` when dealing with `UTF-8` or other variable-width char type, but, anyway, it can still be used as a "container" for text, and if iteration (or other unicode operation) is needed, other libraries such as [UTFCPP](http://utfcpp.sourceforge.net/) can be used together.  
  
I agree that this does not seem ideal, but also there does not seem to be an "ideal" solution if we consider practicality vs. purity: it really helps *a lot* to port code base (from ASCII to Unicode and from Py2 to Py3) that use `std::string`/`std::wstring` everywhere to just consider that they are holding the corresponding UTF encoding, and do the proper conversions on the few boundaries, and correcting "char counting" and "char iteration" in the few places it usually occurs (e.g., in Linux, UTF-8 can usually be passed directly to system/filesystem function, and, in Windows, the same is valid for UTF-16; this is also valid for a lot of libraries).

---

## Comment 13

> Username: nicoddemus  
> Created_at: 2017-01-04 12:22:12 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270359065  

> My point isn't about this patch, it is about the intent of std::string. Its API clearly suggests a fixed-width character type, a requirement which UTF-8 doesn't meet.  
  
I see what you mean, thanks.   
  
I agree the API assumes fixed-width characters, but I've seen `std::string` being used to carry binary data as well...  
  
Is there a standard recommendation on how to pass binary and text data around? Using `wstring` everywhere you mean text seems wasteful.

---

## Comment 14

> Username: nicoddemus  
> Created_at: 2017-01-05 01:12:36 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270535323  

Interestingly, [pybind11](https://github.com/pybind/pybind11) went with the approach of considering `std::string` and `const char*` as `UTF-8` encoded strings when converting to/from C++:  
  
http://pybind11.readthedocs.io/en/master/advanced/cast/overview.html#conversion-table  
  
[Here's the relevant code](https://github.com/pybind/pybind11/blob/master/include/pybind11/cast.h#L607) for those interested.

---

## Comment 15

> Username: stefanseefeld  
> Created_at: 2017-01-05 01:20:41 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-270537063  

Thanks for the reference, that's indeed very useful to know. Still, I maintain my point that `std::string` shouldn't be (ab-)used for UTF-8. Perhaps we should distinguish between `std::string` and `char *` conversion.

---

## Comment 16

> Username: wythend  
> Created_at: 2017-02-16 09:27:23 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-280278746  

@tadeu   
where can i use these code to recompile boost.python  making default encode method with 'gbk' instead of 'utf-8'?  
boost::python::set_default_string_encoding("gbk") and boost::python::set_default_char_pointer_encoding("gbk")).

---

## Comment 17

> Username: tadeu  
> Created_at: 2017-02-16 15:12:03 UTC  
> Updated_at: 2017-02-16 15:12:41 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-280357222  

@wythend, this is our patch:  
  
```patch  
index c2e01c0..c39d5d7 100644  
--- boost/python/converter/builtin_converters.hpp  
+++ boost/python/converter/builtin_converters.hpp  
@@ -13,6 +13,9 @@  
 # include <complex>  
 # include <boost/limits.hpp>  
   
+#define BOOST_PYTHON_FORCE_UNICODE  
+  
 // Since all we can use to decide how to convert an object to_python  
 // is its C++ type, there can be only one such converter for each  
 // type. Therefore, for built-in conversions we can bypass registry  
@@ -156,6 +159,10 @@ BOOST_PYTHON_TO_PYTHON_BY_VALUE(unsigned BOOST_PYTHON_LONG_LONG, ::PyLong_FromUn  
 BOOST_PYTHON_TO_PYTHON_BY_VALUE(char, converter::do_return_to_python(x), &PyUnicode_Type)  
 BOOST_PYTHON_TO_PYTHON_BY_VALUE(char const*, converter::do_return_to_python(x), &PyUnicode_Type)  
 BOOST_PYTHON_TO_PYTHON_BY_VALUE(std::string, ::PyUnicode_FromStringAndSize(x.data(),implicit_cast<ssize_t>(x.size())), &PyUnicode_Type)  
+#elif defined(BOOST_PYTHON_FORCE_UNICODE)  
+BOOST_PYTHON_TO_PYTHON_BY_VALUE(char, converter::do_return_to_python(x), &PyString_Type)  
+BOOST_PYTHON_TO_PYTHON_BY_VALUE(char const*, converter::do_return_to_python(x), &PyString_Type)  
+BOOST_PYTHON_TO_PYTHON_BY_VALUE(std::string, ::PyUnicode_FromStringAndSize(x.data(),implicit_cast<ssize_t>(x.size())), &PyUnicode_Type)  
 #else  
 BOOST_PYTHON_TO_PYTHON_BY_VALUE(char, converter::do_return_to_python(x), &PyString_Type)  
 BOOST_PYTHON_TO_PYTHON_BY_VALUE(char const*, converter::do_return_to_python(x), &PyString_Type)  
diff --git libs/python/src/converter/builtin_converters.cpp libs/python/src/converter/builtin_converters.cpp  
index 1c28af7..cde46f8 100644  
--- libs/python/src/converter/builtin_converters.cpp  
+++ libs/python/src/converter/builtin_converters.cpp  
@@ -366,7 +366,7 @@ namespace  
       static PyTypeObject const* get_pytype() { return &PyFloat_Type;}  
   };  
   
-#if PY_VERSION_HEX >= 0x03000000  
+#if PY_VERSION_HEX >= 0x03000000 || defined(BOOST_PYTHON_FORCE_UNICODE)  
   unaryfunc py_unicode_as_string_unaryfunc = PyUnicode_AsUTF8String;  
 #endif  
   
@@ -379,14 +379,16 @@ namespace  
 #if PY_VERSION_HEX >= 0x03000000  
           return (PyUnicode_Check(obj)) ? &py_unicode_as_string_unaryfunc :   
                   PyBytes_Check(obj) ? &py_object_identity : 0;  
+#elif defined(BOOST_PYTHON_FORCE_UNICODE)  
+          return (PyUnicode_Check(obj)) ? &py_unicode_as_string_unaryfunc : 0;  
 #else  
           return (PyString_Check(obj)) ? &obj->ob_type->tp_str : 0;  
   
 #endif  
       };  
   
-      // Remember that this will be used to construct the result object   
-#if PY_VERSION_HEX >= 0x03000000  
+      // Remember that this will be used to construct the result object  
+#if PY_VERSION_HEX >= 0x03000000 || defined(BOOST_PYTHON_FORCE_UNICODE)  
       static std::string extract(PyObject* intermediate)  
       {  
           return std::string(PyBytes_AsString(intermediate),PyBytes_Size(intermediate));  
```  
  
but you'll probably have to use `PyUnicode_FromEncodedObject` instead of `PyUnicode_FromStringAndSize`.  
  
Also note that this is only for Python 2.

---

## Comment 18

> Username: wythend  
> Created_at: 2017-02-23 16:21:05 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-282041052  

THANK YOU VERY MUCH. I will try it and read it until i understand how types convert between c++ and python.

---

## Comment 19

> Username: earonesty  
> Created_at: 2019-10-31 19:08:24 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-548525505  

At the very least it should be easy to *optionally* return/receive/handle bytes.   IE:  built-in encode/decode from some boost/py bytes vector.

---

## Comment 20

> Username: mikepurvis  
> Created_at: 2020-02-29 02:27:39 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-592821329  

+1 to this. I have a library with functions returning arbitrary binary in `std::string` which comes into boost_python and immediate throws `UnicodeDecodeError`. AFAICT my only option is to separately wrap all the functions on the C++ side and return a `boost::python::object` of the correct type directly.

---

## Comment 21

> Username: centerionware  
> Created_at: 2020-05-24 06:03:06 UTC  
> Updated_at: 2020-05-24 06:05:25 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-633184508  

Let's talk about basic data-types for a second.   
int is either 2 or 4 bytes.   
float is usually 4 bytes but there's really no official definition of the size (unless the new standards defined it),  
double is usually 8 bytes ( same rules for float),  
short is 2 bytes  
long is 8 bytes  
char is 1 byte.  
These are our basic, most basic data types.   
std::string is - a string - of chars. std::string is a string - of bytes.  
Where does it say that std::string is unicode? or ascii? or whatever? because you use it only that way defines it as such? if I'm wrong please point me to the page in the standard that says so, I don't want your opinion, I want the reference from the standard.   
  
struct string { char*s, size_t si} (most basic string) is the very most basic we can break it down to for example purpose only (obviously the standard library is more detailed)  
the only thing we have that represent bytes as bytes, are char's, and in c++ we use std::string to represent multiple bytes in a simple manor with memory management done for us. that's the truth, regardless of how you feel it should only hold unicode or ascii.   
  
I have used this patch for a few years now, others may use it or want to. It should be a #ifdef to change behaviour, or some 'std:text' or 'std::unicode' (which curiously I've never heard of) should be used for only text or unicode. it sucks that wstring was created for 'wide strings' that can contain characters that are multi-byte and implemented differently across os's. but std::string::size() returns the bytes in a string. std::string::c_str() returns it as char* (not null terminated). c++ strings are not made for text, they're made for arbitrary data often used for text.

---

## Comment 22

> Username: lightmare  
> Created_at: 2020-05-25 12:34:17 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-633550395  

@centerionware minor correction: `std::string::c_str()` does return a NUL-terminated string, because one purpose of this function, which gave it its name, is passing strings to C library functions.  
  
But other than that I agree with you. `std::string` has always been used to hold anything byte-oriented, text in any encoding or binary data. As such, the default conversion should not imply an encoding. `std::string` should convert to `bytes`. `std::u(8|16|32)string` can convert to `str`.

---

## Comment 23

> Username: centerionware  
> Created_at: 2020-05-25 17:11:05 UTC  
> Url: https://github.com/boostorg/python/pull/54#issuecomment-633656345  

You're right, I had to look that one up. It's std::string::data() that doesn't guarantee a null termination, where c_str() does.

---
