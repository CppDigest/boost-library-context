# #751 - Boost 1.80.0 JSON error [Closed]

> Username: g2px1  
> Created at: 2022-09-07 08:49:56 UTC  
> Updated at: 2023-07-17 15:56:04 UTC  
> Closed at: 2023-07-17 15:56:04 UTC  
> Url: https://github.com/boostorg/json/issues/751  

/usr/local/include/boost/json/impl/object.ipp:34: std::pair<boost::json::key_value_pair*, long unsigned int> boost::json::detail::find_in_object(const boost::json::object&, CharRange) [with CharRange = boost::core::basic_string_view<char>]: Assertion `obj.t_->capacity > 0' failed.  
  
Unfortunately, I can't provide steps to represent error due I've found this error in logs but I hope it would be helpful. Also can give link to project where this error occurred but project has a complex structure

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-07 14:34:17 UTC  
> Url: https://github.com/boostorg/json/issues/751#issuecomment-1239474685  

Does `object::find` work when the container is default constructed?

---

## Comment 2

> Username: g2px1  
> Created at: 2022-09-07 20:18:29 UTC  
> Updated at: 2022-09-07 20:19:03 UTC  
> Url: https://github.com/boostorg/json/issues/751#issuecomment-1239829815  

> Does `object::find` work when the container is default constructed?  
  
Manually I'm not using this function, maybe it's using by boost::json's functions. I can send you link to repository(and files which are using boost) with my code by email

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-07 21:42:43 UTC  
> Url: https://github.com/boostorg/json/issues/751#issuecomment-1239924672  

This is more a question for @grisumbras , as it is easily tested.

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-09-08 07:28:46 UTC  
> Url: https://github.com/boostorg/json/issues/751#issuecomment-1240334386  

Yeah, extra info is necessary to investigate this issue.

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-07-17 15:56:04 UTC  
> Url: https://github.com/boostorg/json/issues/751#issuecomment-1638423620  

Since I can't do anything with only this information, I'm closing the issue.
