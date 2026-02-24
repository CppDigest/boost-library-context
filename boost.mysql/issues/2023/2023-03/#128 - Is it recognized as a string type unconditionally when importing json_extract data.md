# #128 - Is it recognized as a string type unconditionally when importing json_extract data? [Closed]

> Username: lazychase  
> Created at: 2023-03-16 23:54:54 UTC  
> Updated at: 2023-03-22 16:30:15 UTC  
> Closed at: 2023-03-22 16:30:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/128  

Let's say I have a json column like this:  
  
> {  
>    "strValue":"apple",  
>    "numberValue":1220  
> }  
  
If I do a query like this  
  
> SELECT   
>  JSON_EXTRACT(myField, '$.strValue')   
> , JSON_EXTRACT(myField, '$.numberValue')   
  
When I try to read the numeric value, it recognizes the type as string type.  
  
  
> row[1].as_int64()  
  
> BOOST_CXX14_CONSTEXPR std::int64_t boost::mysql::field_view::as_int64() const  
> {  
>     if (is_field_ptr())  
>         return repr_.field_ptr->as<std::int64_t>();  
>     check_kind(internal_kind::int64);  // ---> It is confirmed as string type.  
>     return repr_.int64;  
> }  
>

---

## Comment 1

> Username: lazychase  
> Created at: 2023-03-17 00:16:01 UTC  
> Url: https://github.com/boostorg/mysql/issues/128#issuecomment-1472928523  

Additionally, if I didn't add an alias to the field, I couldn't read the value properly.  
  
> SELECT  
> JSON_EXTRACT(myField, '$.strValue') as a  
> , JSON_EXTRACT(myField, '$.numberValue') as b  
  
  
To recap, I have two questions.  
1. json-related types are unconditionally returned as string types.  
2. When querying the json type, you must attach an alias for it to work.

---

## Comment 2

> Username: lazychase  
> Created at: 2023-03-17 01:43:58 UTC  
> Url: https://github.com/boostorg/mysql/issues/128#issuecomment-1472988832  

I also needed to be careful when attaching a nickname.  
  
When I search both non-json fields and json fields at the same time, even if I attach an alias only to the json field, I couldn't get the value properly.  
  
> SELECT not_json_field, json_field as a  ==> The value of json_field is not retrieved properly.  
> SELECT not_json_field as a, json_field as b  ==> In this way, all aliases must be attached to properly retrieve values.

---

## Comment 3

> Username: anarthal  
> Created at: 2023-03-19 18:19:19 UTC  
> Url: https://github.com/boostorg/mysql/issues/128#issuecomment-1475352527  

Hi @lazychase,  
  
First of all, which version of MySQL/MariaDB are you using? JSON differs greatly between MySQL and MariaDB.  
  
Question 1: when you do `SELECT JSON_EXTRACT(myField, '$.numberValue')`, the type of the generated field is still JSON, as given by `boost::mysql::column_type::json`. Fields with a JSON type are always represented as strings in `field` and `field_view`, since they main contain any arbitrary JSON value. This is documented [in this table](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/fields.html#mysql.fields.mysql_to_c_type_mappings).  
  
If you're completely sure that `JSON_EXTRACT(myField, '$.numberValue')` always yields an integer value for your schema, then I suggest you to modify your query to `SELECT CAST(JSON_EXTRACT(f1, '$.numberValue') AS SIGNED)`, which will cause MySQL to cast the extracted value to an integer, so that you can call `field_view::as_int64()` on it.  
  
Question 2: I can't reproduce. I need more information - what does "I couldn't read the value properly" mean? Which functions are you calling, and what behavior are you observing (exception, crash...)?  
  
Regards,  
Ruben.

---

## Comment 4

> Username: chaseYLC  
> Created at: 2023-03-22 01:35:56 UTC  
> Url: https://github.com/boostorg/mysql/issues/128#issuecomment-1478802991  

>  suggest you to modify your query to SELECT CAST(JSON_EXTRACT(f1, '$.numberValue') AS SIGNED), which will cause MySQL to cast the extracted value to an integer, so that you can call field_view::as_int64() on it  
  
thanks. this was helpful to me.  
  
  
about Question 2>> When I did not specify an alias for all return items, there was a phenomenon that the json field value returned an empty string. This seems to be the result of my poor stack management.  
  
I am using boost-json library, predefined BOOST_JSON_STACK_BUFFER_SIZE is 4096, The problem seems to have arisen as the value of the json field I manage is very large.

---

## Comment 5

> Username: anarthal  
> Created at: 2023-03-22 16:30:15 UTC  
> Url: https://github.com/boostorg/mysql/issues/128#issuecomment-1479894849  

Cool. If this solved both of your questions, I will close the issue. Thanks for using the library.
