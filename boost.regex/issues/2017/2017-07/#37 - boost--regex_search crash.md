# #37 - boost::regex_search crash [Closed]

> Username: siten  
> Created at: 2017-07-30 13:09:13 UTC  
> Updated at: 2017-07-31 06:12:20 UTC  
> Closed at: 2017-07-30 18:11:22 UTC  
> Url: https://github.com/boostorg/regex/issues/37  

Using the regular expression "<pre.*class=.+id=.+>([\\s\\S]+)</pre>" to search  crash.txt  
 file, boost::regex_search function will be very slow and will crash.  
[crash.txt](https://github.com/boostorg/regex/files/1185690/crash.txt)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-07-30 18:11:22 UTC  
> Url: https://github.com/boostorg/regex/issues/37#issuecomment-318919215  

It doesn't crash: it throws an exception to indicate that backtracking was taking excessive time.  
  
There is an error in your expression as well: if this is for embedding in C++ code, then the string should be: "<pre.*class=.+id=.+>([\\s\\S]+)" since C++ gobbles the first "\".  An alternative to avoid this is to use raw string literals.  If you do this then the expression behaves fine.  
  
BTW I tried your expression (the "<pre.*class=.+id=.+>([sS]+)" variant, which is what you were actually trying, even if this wasn't what you intended) at https://regex101.com/ with pcre and "single line" mode activated (Boost Regex default), and it bombs out as well with:  
  
_"Catastrophic backtracking has been detected and the execution of your expression has been halted. To find out more what this is, please read the following article: Runaway Regular Expressions  
  
I recommend you launch the debugger in the menu to the left and analyze the data to find out the cause."_

---

## Comment 2

> Username: siten  
> Created at: 2017-07-31 06:09:41 UTC  
> Url: https://github.com/boostorg/regex/issues/37#issuecomment-318976941  

I'm sorry I was wrong to write the regular expression "<pre.*class=.+id=.+>([\\s\\S]+)" . I tested "<pre.*class=.+id=.+>([\\\\s\\\\S]+)<\\\\/pre>" in my C++ code boost::regex_search still throws an exception.  
In addition, "<pre.*class=.+id=.+>([sS]+)<\\\\/pre>" can not match the results I want.  
Use the STL regx or python with "<pre.*class=.+id=.+>([\\\\s\\\\S]+)<\\\\/pre>" i can get the results.

---

## Comment 3

> Username: siten  
> Created at: 2017-07-31 06:12:20 UTC  
> Url: https://github.com/boostorg/regex/issues/37#issuecomment-318977408  

thank you.
