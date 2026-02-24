# #223 - The contract of const_string [Closed]

> Username: akrzemi1  
> Created at: 2022-07-07 10:11:30 UTC  
> Updated at: 2022-07-26 20:47:12 UTC  
> Closed at: 2022-07-26 20:47:12 UTC  
> Url: https://github.com/boostorg/url/issues/223  

I have a problem with understanding the contract of `const_string`.   
  
The docs say ([see here](https://master.url.cpp.al/url/ref/boost__urls__const_string.html)):   
  
> A *constant string* with shared ownership.  
  
My understanding of this statement is that it behaves as `const std::string`. The contract of `const std::string` can be expressed with the following assertion:  
  
```c++  
const std::string s = "ABCD";  
any_operation(s);  
assert(s == "ABCD");  
```  
  
However, `const_string` does not satisfy this expectation:  
  
```c++  
const_string s("ABCD", std::allocator<char>{});  
s.remove_suffix(2);  // allowed mutating operation  
assert(s == "ABCD"); // fails!  
```  
  
`const_string` allows the modification of its value -- to some extent: you are not allowed to mutate the individual characters, but you are allowed to select the arbitrary subrange of what you already have. More, `const_string` can store a const buffer of characters but represent a value from a completely unrelated buffer:  
  
```c++  
void test(bool cond1, bool cond2, bool cond3)  
{  
  urls::string_view* selected_string = nullptr;  
  urls::const_string variant1 ("ABCD", std::allocator<char>{});  
  urls::const_string variant2 ("1234", std::allocator<char>{});  
    
  if (cond1)  
    selected_string = &variant1;  
  else if (cond2)  
    selected_string = &variant2;  
      
  if (selected_string && cond3)  
    *selected_string = "PQRS";  
  
  std::cout << variant1 << std::endl;  
};  
```  
  
Calling `test(true, true, true)` prints "PQRS" which means the value of `variant1` changed, even though the contents of its buffer did not.  
  
So, the semantics of `const_buffer` is "a `string_view` with an auxiliary (const) buffer which you may (but do not have to) use". It looks like neither the name nor the description in the docs reflects this.  
    
I understand that the use case that `const_string` addresses is: return a new string cheaply, minimizing allocations, for the purpose of either reading or converting to other string type.  `const_string` does satisfy this goal, but as a stand-alone type it doesn't have a clear design. I guess there is a number of ways forward. One would be to alter the design. Another would be not to advertise the type in the library: just say that functions like `segments()`  return an library-implementation-detail type that guarantees only the non-mutating subset of `string_view` operations and conversion to other string-types (anything else is not guaranteed: either to compile or to execute).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-07 12:21:32 UTC  
> Updated at: 2022-07-07 12:22:07 UTC  
> Url: https://github.com/boostorg/url/issues/223#issuecomment-1177523319  

Mamma mia! This `const_string` is giving me a headache _this big_  
  
![image](https://user-images.githubusercontent.com/1503976/177772011-661904df-cbe0-498f-b7e2-b9bcd2878c4f.png)

---

## Comment 2

> Username: akrzemi1  
> Created at: 2022-07-08 19:16:30 UTC  
> Url: https://github.com/boostorg/url/issues/223#issuecomment-1179291523  

Another twisted example:  
  
```c++  
int main()  
{   
  urls::const_string b ("B", std::allocator<char>{});  
  urls::const_string a ("A", std::allocator<char>{});  
  
  {  
    urls::string_view av = a;  
    urls::string_view bv = b;  
    static_cast<urls::string_view&>(b) = av;  
    static_cast<urls::string_view&>(a) = bv;  
  }  
  
  std::cout << a << std::endl;  // "B"  
  std::cout << b << std::endl;  // "A"  
}  
```  
  
and we have `a` pointing to the buffer of `b`, and `b` pointing to the buffer of `a`.  
  
On a more serious note, though, it looks like using the mutating interface of `const_string` is never an UB. It has a well defined and predictable behavior, once you understand what it is.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-07-09 19:21:57 UTC  
> Url: https://github.com/boostorg/url/issues/223#issuecomment-1179594050  

> and we have a pointing to the buffer of b, and b pointing to the buffer of a.  
  
😱  
  
My head hurts just thinking of the implications of that.  
  
> it looks like using the mutating interface of const_string is never an UB  
  
Which is very weird for something called `const_string`
