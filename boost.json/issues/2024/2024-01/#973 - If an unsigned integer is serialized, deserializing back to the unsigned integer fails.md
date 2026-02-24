# #973 - If an unsigned integer is serialized, deserializing back to the unsigned integer fails [Closed]

> Username: DUOLabs333  
> Created at: 2024-01-17 21:34:16 UTC  
> Updated at: 2024-01-22 13:55:07 UTC  
> Closed at: 2024-01-22 13:55:07 UTC  
> Url: https://github.com/boostorg/json/issues/973  

When reporting a bug please include the following:  
  
### Version of Boost  
1.84  
### Steps necessary to reproduce the problem  
1. Serialize `uint8_t test=8;`  
2. Parse it back into a value `value test_value=serialize(value_from(test));`  
3. Try to get the number with `value_to<uint8_t>(test_value)`  
4. See the exception `Not exact`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-01-17 22:04:19 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1897030490  

Step 2 doesn't compile for me at all. Can you please provide an actual minimal, complete and verifiable example (MCVE)?  
  
Although, let's consider this one:  
```c++  
#include <boost/json.hpp>  
#include <cassert>  
  
int main()  
{  
        uint8_t test = 8;  
        value test_value = value( serialize(value_from(test)) ); // (1); notice explicit constructor  
        auto test2 = value_to<uint8_t>(test_value); // (2)  
        assert( test == test2 );  
        return 0;  
}  
```  
  
Here (1) converts the integer `test` to `json::value`, then serializes that into `std::string`, then assigns *that string* to a new `json::value`. This is equivalent to doing `value test_value = "8";`   
  
(2) tries to convert _that JSON string_ into a number, and predictably fails. But the error is "not a number".  
  
Then, I guess, this is still not something that you are doing. So, please provide an MCVE.

---

## Comment 2

> Username: DUOLabs333  
> Created at: 2024-01-17 22:21:33 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1897120239  

This replicates it:  
```  
#include <boost/json/src.hpp>  
using namespace boost::json;  
#include <cassert>  
  
int main()  
{		  
		object test;  
        test["value"]=160;  
        object test_new = parse( serialize(test) ).as_object(); // (1); notice explicit constructor  
        auto test2 = value_to<char>(test_new["value"]);  
        printf("%d\n",test2);  
        return 0;  
}  
```

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-01-17 22:24:08 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1897133714  

Seems like `160` is outside of the domain of your implmentation's `char`. This implies, that your char is signed and has the domain [-128;127].

---

## Comment 4

> Username: DUOLabs333  
> Created at: 2024-01-17 22:28:50 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1897157496  

Oh --- as it turns out, the previous library I used (nlohmann) silently failed, so I didn't notice that this was an issue.

---

## Comment 5

> Username: DUOLabs333  
> Created at: 2024-01-17 22:59:13 UTC  
> Updated at: 2024-01-17 22:59:19 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1897303351  

Is there a way to get the same behavior and ignore the exception (I'm OK with the values wrapping around)?

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-01-22 10:27:30 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1903687167  

```c++  
auto c = static_cast<char>( value_to<int>(test_new["value"]) );  
```

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-01-22 10:51:55 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1903741644  

If you actually have a more complex structure, where the char is deep inside another object, you would have to use contextual conversions. See an example here: https://godbolt.org/z/TK4dhWPEr

---

## Comment 8

> Username: DUOLabs333  
> Created at: 2024-01-22 13:55:07 UTC  
> Url: https://github.com/boostorg/json/issues/973#issuecomment-1904053734  

Thanks!
