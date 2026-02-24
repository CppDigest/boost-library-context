# #588 - Some Boost.Spirit source files are not UTF-8 [Closed]

> Username: skelly-energid  
> Created at: 2020-04-14 15:06:02 UTC  
> Updated at: 2020-05-04 16:03:02 UTC  
> Closed at: 2020-05-04 16:03:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/588  

```  
Python 3.7.5 (default, Nov 20 2019, 09:21:52)   
Type "copyright", "credits" or "license" for more information.  
  
IPython 5.8.0 -- An enhanced Interactive Python.  
?         -> Introduction and overview of IPython's features.  
%quickref -> Quick reference.  
help      -> Python's own help system.  
object?   -> Details about 'object', use 'object??' for extra details.  
  
In [1]: f = open("./boost/spirit/home/support/char_encoding/iso8859_1.hpp", "r")  
  
In [2]: f.read()  
---------------------------------------------------------------------------  
UnicodeDecodeError                        Traceback (most recent call last)  
<ipython-input-2-571e9fb02258> in <module>()  
----> 1 f.read()  
  
/usr/lib/python3.7/codecs.py in decode(self, input, final)  
    320         # decode input (taking the buffer into account)  
    321         data = self.buffer + input  
--> 322         (result, consumed) = self._buffer_decode(data, self.errors, final)  
    323         # keep undecoded input until the next call  
    324         self.buffer = data[consumed:]  
  
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xa1 in position 10012: invalid start byte  
  
In [3]: f = open("./boost/spirit/home/x3/support/subcontext.hpp", "r")  
  
In [4]: f.read()  
---------------------------------------------------------------------------  
UnicodeDecodeError                        Traceback (most recent call last)  
<ipython-input-4-571e9fb02258> in <module>()  
----> 1 f.read()  
  
/usr/lib/python3.7/codecs.py in decode(self, input, final)  
    320         # decode input (taking the buffer into account)  
    321         data = self.buffer + input  
--> 322         (result, consumed) = self._buffer_decode(data, self.errors, final)  
    323         # keep undecoded input until the next call  
    324         self.buffer = data[consumed:]  
  
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xed in position 151: invalid continuation byte  
```

---

## Comment 1

> Username: djowel  
> Created at: 2020-04-14 22:52:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/588#issuecomment-613719939  

Will you do a PR?

---

## Comment 2

> Username: skelly-energid  
> Created at: 2020-04-15 09:33:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/588#issuecomment-613929872  

I'm afraid not.

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-05-04 15:26:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/588#issuecomment-623531870  

@K-ballo could you please send a PR with your name transliterated? I am asking you about this to have done it right and to clear potential legal problems of such actions.

---

## Comment 4

> Username: K-ballo  
> Created at: 2020-05-04 15:29:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/588#issuecomment-623533378  

> @K-ballo could you please send a PR with your name transliterated  
  
@Kojoley feel free to use the ascii variant "Agustin Berge", as should already be the case in other source files.
