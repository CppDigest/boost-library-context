# #82 - Different behaviour for .* and derivates on emoji sequence [Closed]

> Username: dcsjulio  
> Created at: 2019-04-19 18:31:15 UTC  
> Updated at: 2019-04-20 17:08:11 UTC  
> Closed at: 2019-04-20 17:08:11 UTC  
> Url: https://github.com/boostorg/regex/issues/82  

First of all, sorry because I cannot provide a simple C++ example and I'm using an application that uses Boost instead.  
  
Notepad++, which uses Boost regex libs, seems to have trouble with emojis and regex.  
  
The emoji sequence "👩‍❤️‍💋‍👩" does not seem to be rendered as a sequence on notepad++ It is rendered as 4 characters: 👩❤️💋👩  
  
When doing a regex search, `.*` seems to match all characters, but `(?:.)*` does not.  
  
For example, given this input: `foobar👩‍❤️‍💋‍👩` or `foobar👩❤️💋👩` in notepad++  
  
`.*` will match everything (`foobar👩❤️💋👩`)  
  
`(?:.)*` will match `foobar` foollowed by several zero-width matches between the emojis.  
  
Also, `[\s\S]` will behave the same way as `(?:.)*`.  
  
Thank you.

---

## Comment 1

> Username: dcsjulio  
> Created at: 2019-04-20 17:08:11 UTC  
> Url: https://github.com/boostorg/regex/issues/82#issuecomment-485143707  

Closing because It seems to be related just to notepad++ or some old boost lib issue. Other tools that use recent version of boost lib do not have the problem.
