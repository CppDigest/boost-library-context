# #983 - Add a convenience member function to call value_to [Closed]

> Username: kiwixz  
> Created at: 2024-02-13 17:53:31 UTC  
> Updated at: 2024-10-09 14:02:59 UTC  
> Closed at: 2024-10-09 14:02:59 UTC  
> Url: https://github.com/boostorg/json/issues/983  

Hi, first of all thanks for the great library fast yet easy to use.  
  
I'm currently writing tag_invoke functions to convert JSON values from/to a class, and I have something like this:  
```cpp  
MyClass{json.at("superint1").to_number<int>(),  
        json.at("superint2").to_number<int>(),  
        json.at("superbool").as_bool(),  
        json.at("superstring").as_string(),  
        boost::json::value_to<std::vector<int>>(json.at("supervectorofint"))}  
```  
  
It's a class with private values and a constructor, so I cant just rely on Boost Describe for this.  
  
If there was a `to<T>()` member function (just calling `boost::json::value_to` under the hood), I could have arguably more consistent code:  
```cpp  
MyClass{json.at("superint1").to_number<int>(),  
        json.at("superint2").to_number<int>(),  
        json.at("superbool").as_bool(),  
        json.at("superstring").as_string(),  
        json.at("supervectorofint").to<std::vector<int>>()}  
```  
  
And it could also allow something ever better (to my eyes that is!):  
```cpp  
MyClass{json.at("superint1").to<int>(),  
        json.at("superint2").to<int>(),  
        json.at("superbool").to<bool>(),  
        json.at("superstring").to<std::string>(),  
        json.at("supervectorofint").to<std::vector<int>>()}  
```  
  
I like the fact that there is a fixed set of `as_*` functions, guaranteeing direct access to the underlying value; but value_to seems to be the "do whatever you need but get me this" convenience method I usually want.  
  
Related to #418.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-13 18:05:07 UTC  
> Url: https://github.com/boostorg/json/issues/983#issuecomment-1942118162  

You can still use Describe if you declare an intermediate struct holding the parameters.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-02-17 10:39:05 UTC  
> Url: https://github.com/boostorg/json/issues/983#issuecomment-1949930773  

On second thought, isn't  
```  
MyClass{json.at("superint1").to<int>(),  
        json.at("superint2").to<int>(),  
        json.at("superbool").to<bool>(),  
        json.at("superstring").to<std::string>(),  
        json.at("supervectorofint").to<std::vector<int>>()}  
```  
possible to write today as  
```  
MyClass{  
        value_to<int>(json.at("superint1")),  
        value_to<int>(json.at("superint2")),  
        value_to<bool>(json.at("superbool")),  
        value_to<std::string>(json.at("superstring")),  
        value_to<std::vector<int>>(json.at("supervectorofint"))  
}  
```  
?

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-02-17 10:44:29 UTC  
> Url: https://github.com/boostorg/json/issues/983#issuecomment-1949932505  

It is. I think, @kiwixz considers that syntax too cumbersome.  
  
To be honest, I'm not fond of adding just another way to call `value_to`.

---

## Comment 4

> Username: kiwixz  
> Created at: 2024-02-17 23:33:10 UTC  
> Url: https://github.com/boostorg/json/issues/983#issuecomment-1950548448  

I didn't think of `using namespace` here which helps a lot.  
  
It still feels weird to me to use it as a free function, especially when we have `to_number` as a member.

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-02-19 13:28:35 UTC  
> Updated at: 2024-02-19 13:28:45 UTC  
> Url: https://github.com/boostorg/json/issues/983#issuecomment-1952456473  

True, but making an equivalent of `value_to`  member function of `value` is much more intrusive.
