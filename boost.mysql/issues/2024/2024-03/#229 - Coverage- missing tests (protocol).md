# #229 - Coverage: missing tests (protocol) [Open]

> Username: anarthal  
> Created at: 2024-03-14 11:55:51 UTC  
> Updated at: 2024-03-14 11:56:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/229  

* compute_column_type json (MariaDB specific?)  
* deserialize_execute_response marks some error codes as not hit  
* deserialize_text_ymd: separator  
* deserialize_text_value_date: excess characters  
* deserialize_text_value_datetime: separator  
* deserialize_text_value_time: separator  
* deserialize_text_value_float: res.ptr != end  
* deserialize_binary_field_string: error  
* should_perform_full_auth: branch  
* next_char_utf8mb4: not enough size checks  
  
See if we could write some of these in a more coverage-friendly way.
