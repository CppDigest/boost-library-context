# #296 - javadoccing hidden friends correctly [Closed]

> Username: alandefreitas  
> Created at: 2022-08-02 17:25:44 UTC  
> Updated at: 2022-08-03 22:17:14 UTC  
> Closed at: 2022-08-03 22:17:14 UTC  
> Url: https://github.com/boostorg/url/issues/296  

When we write  
  
```cpp  
struct T  
{  
  /** Hey goys!  
  */  
  friend ostream& operator<<( ostream&, T const & );  
};  
```  
  
Doxygen does not see this as a global declaration, and thus there is no overload resolution landing page.  
  
This has to be written as  
  
```cpp  
struct T  
{  
  friend ostream& operator<<( ostream&, T const & )  
  {  
    ...  
  }  
};  
  
/** Hey goys!  
*/  
ostream& operator<<( ostream&, T const & );  
```  
  
Then Doxygen will see the symbol

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-02 17:38:35 UTC  
> Url: https://github.com/boostorg/url/issues/296#issuecomment-1203026553  

This commit fixes `operator<<` for `url` and `url_view`:  
https://github.com/vinniefalco/url/commit/d26137e927566bdeb8d027ada382865fa7e83cbc

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-03 22:17:14 UTC  
> Url: https://github.com/boostorg/url/issues/296#issuecomment-1204540997  

merged
