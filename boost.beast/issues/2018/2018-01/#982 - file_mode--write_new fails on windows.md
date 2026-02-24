# #982 - file_mode::write_new fails on windows [Closed]

> Username: chrisics  
> Created at: 2018-01-15 12:38:05 UTC  
> Updated at: 2019-01-02 02:32:19 UTC  
> Closed at: 2019-01-02 02:32:19 UTC  
> Url: https://github.com/boostorg/beast/issues/982  

Due to what seems to be compliance issue of the standard c library of Microsoft,   
The call open() of file_stdio, with file_mode "write_new" translates to a call to   
  
std::fopen (file_name, "wbx");  
  
And this fails with "invalid argument passed to c-function".   
  
The "x" is not supported: See https://msdn.microsoft.com/en-us/library/yeby3zcb.aspx  
  
(Beast version 144)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-15 12:58:09 UTC  
> Url: https://github.com/boostorg/beast/issues/982#issuecomment-357675801  

Can you please submit a pull request with a fix?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-01-02 02:32:19 UTC  
> Url: https://github.com/boostorg/beast/issues/982#issuecomment-450777814  

This should be fixed now
