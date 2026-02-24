# #484 - Parsing issue [Closed]

> Username: jano42  
> Created at: 2021-01-13 10:54:48 UTC  
> Updated at: 2021-03-01 19:30:48 UTC  
> Closed at: 2021-03-01 19:30:48 UTC  
> Url: https://github.com/boostorg/json/issues/484  

Hello,  
  
I'm trying to port my application ([Yadoms](https://github.com/Yadoms/yadoms)) which use rapidjson, to your new boost::json  
  
I find a parsing issue when writing my unit tests.  
  
I try to parse a decimal value (18.4 from my input string) and then retreive value as double.  
  
My sample code  
  
```c++  
      const std::string data("{"  
         "\"DecimalParameter\": 18.4"  
         "}");  
  
      auto& jv = boost::json::parse(data);  
      boost::json::value *decimal = jv.get_object().if_contains("DecimalParameter");  
      if (decimal)  
      {  
         BOOST_CHECK_EQUAL(decimal->as_double(), 18.4);  
         BOOST_CHECK_EQUAL(decimal->get_double(), 18.4);  
         BOOST_CHECK_EQUAL(decimal->to_number<double>(), 18.4);  
         BOOST_CHECK_EQUAL(decimal->to_number<float>(), 18.4f);  
      }  
```  
  
The results:  
  
```  
  Message:   
    check decimal->as_double() == 18.4 has failed [18.400000000000002 != 18.399999999999999]  
    check decimal->get_double() == 18.4 has failed [18.400000000000002 != 18.399999999999999]  
    check decimal->to_number<double>() == 18.4 has failed [18.400000000000002 != 18.399999999999999  
```  
  
The parsing is not exact, but works with floats!  
  
  
Environnement:  
 * boost : 1.75.0  
 * VisualStudio 2019 16.8.3  
 * build configuration x86  
  
I mention also @sgallou (other main developer of Yadoms)

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-01-14 14:42:00 UTC  
> Url: https://github.com/boostorg/json/issues/484#issuecomment-760239450  

Did you have a similar test working before (with rapidjson)?

---

## Comment 2

> Username: jano42  
> Created at: 2021-02-01 16:34:01 UTC  
> Url: https://github.com/boostorg/json/issues/484#issuecomment-770985861  

Sorry for delay.  
  
Yes I have exactly the same test working with rapidjson (if you need some details, I can share you, or extract you the working code.)  
  
I've upgraded from rapidjson to boost::json. The unittests are still all active.  
All my tests passed with rapidjson.  
  
This one by parsing a double fail with boost::json.
