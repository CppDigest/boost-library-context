# #95 - Conversion of `char` to python is broken in Python >= 3.0 [Open]

> Username: tadeu  
> Created at: 2016-12-29 19:25:51 UTC  
> Updated at: 2016-12-29 19:26:03 UTC  
> Url: https://github.com/boostorg/python/issues/95  

`char` is currently being converted using `PyUnicode_FromStringAndSize(&x, 1)`, but [PyUnicode_FromStringAndSize](https://docs.python.org/3/c-api/unicode.html#c.PyUnicode_FromStringAndSize) interprets the input string as UTF-8, so every char between 128 and 255 is a malformed UTF-8 string and can't be converted.  
  
Before submitting a fix, I'd like to discuss the solution: returning a positive integer seems to be more appropriate, since this is the new convention in Python 3 (e.g., `b'a'[0]` returns 97 in Python 3, while it returns `b'a'` in Python 2). Also, this is more consistent with how `signed char` and `unsigned char` are currently converted (as integers too).

---

## Comment 1

> Username: tadeu  
> Created at: 2016-12-29 19:26:03 UTC  
> Url: https://github.com/boostorg/python/issues/95#issuecomment-269680078  

cc @nicoddemus
