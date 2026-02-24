# #499 - Better error diagnosis [Closed]

> Username: stream009  
> Created at: 2021-02-10 12:30:55 UTC  
> Updated at: 2021-02-10 12:53:44 UTC  
> Closed at: 2021-02-10 12:53:44 UTC  
> Url: https://github.com/boostorg/json/issues/499  

Hi, thank you for a nice library.  
### Version of Boost  
1.75  
  
In general, when I input 500kB of data into computer and it just says "syntax error", that make me wanna throw a brick at computer.  
  
I would like information from parser which is enough to produce message like this:  
```  
Syntax error at line 20:  
{ "foo, 100 }  
      ^-----  
```  
So far, only information I can get from parser seems to be an error code. I want location (line and column). Calculating line number from position after received a parse error would be difficult, considering escaped newline in strings.  
  
Parser will need to track line number and beginning of line during parsing to produce such information. It might hurt benchmarks a bit, but I believe it is necessary cost.

---

## Comment 1

> Username: stream009  
> Created at: 2021-02-10 12:34:46 UTC  
> Url: https://github.com/boostorg/json/issues/499#issuecomment-776676740  

On second thought, JSON can't have newline in string. That means only position will do.

---

## Comment 2

> Username: stream009  
> Created at: 2021-02-10 12:53:44 UTC  
> Url: https://github.com/boostorg/json/issues/499#issuecomment-776686368  

And I just found out parser::write seems to return the position error occur.  
So I can have enough information. I am going to close this.
