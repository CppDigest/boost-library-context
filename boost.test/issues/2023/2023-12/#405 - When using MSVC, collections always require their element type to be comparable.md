# #405 - When using MSVC, collections always require their element type to be comparable [Open]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2023-12-05 21:28:28 UTC  
> Updated at: 2023-12-05 21:28:28 UTC  
> Url: https://github.com/boostorg/test/issues/405  

```c++  
struct elem;  
//bool operator==(const elem &, const elem &);  
struct container {  
    const elem *begin() const;  
    const elem *end() const;  
};  
bool operator==(const container &, const container &);  
std::ostream &operator<<(std::ostream &, const container &);  
  
void foo(container &c)  
{  
    BOOST_TEST(c == c);  
}  
```  
  
This fails to compile in MSVC unless the `operator==` declaration on the second line is uncommented.  That operator does not need to be defined because it is never actually used, but MSVC will complain about its absence if it is not declared.
