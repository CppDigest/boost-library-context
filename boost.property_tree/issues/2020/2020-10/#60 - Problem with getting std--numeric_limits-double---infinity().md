# #60 - Problem with getting std::numeric_limits<double>::infinity() [Open]

> Username: LuAPi  
> Created at: 2020-10-15 12:59:56 UTC  
> Updated at: 2021-06-10 16:10:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60  

Example code:  
```  
#include <iostream>  
#include <boost/property_tree/info_parser.hpp>  
#include <boost/property_tree/ptree.hpp>  
#include <boost/version.hpp>  
#include <string>  
#include <limits>  
  
  
int main() {  
  std::cout << "Boost Version: " << BOOST_VERSION << std::endl;  
  boost::property_tree::ptree tree;  
  
  tree.put("number", std::numeric_limits<double>::infinity());  
  
  for (auto &[key, value] : tree)  
    std::cout << key << " : " << value.get_value<std::string>() << std::endl;  
  
  auto number = tree.get_optional<double>("number").get();  
  std::cout << "number optional : " << number << std::endl;  
}  
```  
Example output:  
```  
Boost Version: 107400  
number : inf  
test: /usr/include/boost/boost/optional/optional.hpp:1212: boost::optional<T>::reference_type boost::optional<T>::get() [with T = double; boost::optional<T>::reference_type = double&]: Assertion `this->is_initialized()' failed.  
Aborted  
```  
Expected output:  
```  
Boost Version: 107400  
number : inf  
number optional: inf  
```  
  
Changing the `std::string` in the `get_value` to `double` results in:  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::property_tree::ptree_bad_data> >'  
  what():  conversion of data to type "d" failed  
```  
  
Problem also applies to `float` and `long double` types.  
  
Tested in https://hub.docker.com/r/zouzias/boost/  
and https://tio.run/##hVDRSsMwFH3vV1wqSAtze89qHwY@@OLAyV5EStbezWCahDQZFPHb603aqZuCL2lz7jkn59zamJtacnUYhiuhaukbhELozlnkbZl8YztN4MJYbdC6vqI5LoTa68pw26GdvxrzH92E82/iEW0ntLocUgyhDj8RKVrhujJJEqEctFyoLIf3BKBzDWO19g6KAtJVcIXt6MogDeBqvd48Vdu7x839@iEAUYKqkUvSxxyMnSWma/hAOJYJkWIB412WKt/u0Kaz0YRuaEVdjemKRvudxJIxWpBQwvVZnkf9XlvIuHcarp/fsJ/BkUuPL8Cic06MiyJEin1g6hD58wO6Kv4VkTxtiRZxXors4ltjVrgd4wexNo72wuUp6VefPIyzfPlroZPHSXjKM8Hn734Mwyc  
and a system running boost 1.60

---

## Comment 1

> Username: DMaroo  
> Created at: 2021-05-29 06:26:09 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850779650  

This is basically because `boost::property_tree::ptree` first converts the value to `std::string` and then stores it. So in this case, the value `std::numeric_limits<double>::infinity()` gets stored as a string, `"inf"`, in the tree, rather than a double. Now when you use `get_value` or `get_optional` to get that value, boost uses the `stream_translator` class  
  
https://github.com/boostorg/property_tree/blob/cf8189a52dec4a00a616b1ff0a49615c05a332c9/include/boost/property_tree/stream_translator.hpp#L181-L183  
  
to translate the given string to your requested data type, which in this case is a `double`. However, it fails to translate `"inf"` to a double. You can also reproduce a similar error by the following compiling and running the following code  
  
```c++  
#include <iostream>  
#include <boost/property_tree/ptree.hpp>  
  
int main()  
{  
    boost::property_tree::ptree tree;  
    tree.put("number", 4.56);  
      
    auto number = tree.get_optional<int>("number").get();  
    std::cout << number << std::endl;  
}  
```  
  
Again, as explained above, boost is unable to convert the string "4.56" to an `int` (which is, I guess, the expected behavior).  
  
I am not sure whether I should do a PR for this, since I don't whether this was meant to be a design feature rather than a bug. That's why I'm pinging the maintainer (@madmongo1 Hey, Richard! Can I work on this?).  
  
Hope this helps.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-05-29 09:43:34 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850805260  

@DhruvMaroo ,  
  
I think a good place to start would be to define the behaviour we would like to see and investigate whether that breaks any existing assumptions.  
  
Perhaps it's a good idea to summarise here?

---

## Comment 3

> Username: DMaroo  
> Created at: 2021-05-29 09:50:19 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850806048  

Thank you for responding @madmongo1,  
  
I suppose the expected behavior should be that stream extractor recognizes that `"inf"` can be converted to the type `double` or `float`. So, when it encounters `"inf"`, it can translate it to `std::numeric_limits<double>::infinity()` or `std::numeric_limits<float>infinity()` if required. Currently, it just throws an exception mentioning that the conversion has failed. This won't break any other existing code in my opinion (as long as somebody hasn't (ab)used this bug in their code, but again, I'm not totally sure). I guess this is pretty much all we need to do to fix this.

---

## Comment 4

> Username: LuAPi  
> Created at: 2021-05-29 10:11:21 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850808615  

For me the expected behaviour was that I could call `ptree ::put` with an `double input` of any value and then call `ptree ::get_optional<double>` to retrieve that value and assign a `double output` and have `input == output` be equal to `input == input` with no exceptions thrown. I expected the tree to store all double values (except only expected one type of NaN not the different types of NaN), not just most of them.  
  
I've not tested this but I suspect this will fail for NaN as well as +infinity and -infinity.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-05-29 10:12:25 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850808745  

We had a similar conversation in boost.json in the end it was decided that storing a nan or inf was an error since that's not representable in JSON.  
  
i.e.  
  
```  
{  
  "a": inf  
}  
```  
is invalid json and will break any parser.  
  
Are we saying that in the case of `inf`, boost.property_tree will deviate from "expected" behaviour and instead produce a string:  
```  
{  
  "a": "inf"  
}  
```  
?  
  
This feels like an interface change to me (whether it's a welcome one or not).

---

## Comment 6

> Username: LuAPi  
> Created at: 2021-05-29 10:28:28 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850810497  

Ah, I was working with XML so I hadn't considered those values might not be representable in other formats.  
I'd have thought that the exception in that case should occur when trying to serialise those values to JSON rather than in the get_optional call.  
I could work around this by calling `std::stod(tree.get_optional<std::string>("number").get())` instead of `tree.get_optional<double>("number").get()` but it seems a little inelegant.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-05-29 11:41:42 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850818975  

I’m not sure there are any elegant solutions when all formats dont support all data ranges :-S

---

## Comment 8

> Username: DMaroo  
> Created at: 2021-05-29 14:07:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850839346  

@madmongo1 I think we can set a special case for conversion of "inf" and "nan" to a `double` or a `float`. However, I am not completely sure yet, on how we would do it. In any case, would this be a good idea?

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-05-29 14:09:34 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-850839762  

The problem as I see it is that people use property_tree as a json parser and Dom type. (Whether rightly or wrongly, this is in the field)  
  
suddenly changing the behaviour to something nonstandard for json could break these people’s code.

---

## Comment 10

> Username: yost-jacob  
> Created at: 2021-06-10 13:23:27 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-858619506  

If you wanted to consider the idea further:  One may change the default translator to use lexical_cast instead of stringstream, or just create a specialization of the translator that does the same.  Based upon the FAQ it seems to be supported https://www.boost.org/doc/libs/1_76_0/doc/html/boost_lexical_cast/frequently_asked_questions.html  
  
Then we would update the JSON parser to catch the nan or inf.

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-06-10 16:10:15 UTC  
> Url: https://github.com/boostorg/property_tree/issues/60#issuecomment-858753946  

I don't see a fundamental problem with making the parser more accepting of "inf" or "nan". It seems to me that this is unlikely to break any code.  
  
Is there a strong use case for it?
