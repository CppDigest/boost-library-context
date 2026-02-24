# #398 - Should invalid format_options return an error instead? [Closed]

> Username: bruno-viva  
> Created at: 2025-01-15 15:44:41 UTC  
> Updated at: 2025-01-15 23:43:00 UTC  
> Closed at: 2025-01-15 23:43:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/398  

Hi,  
  
I expect some user input in my application. The user input is just an ID, which should be a valid string. However, I didn't specify any UTF-8 requirements, and in my tests I was double checking what the error looks like for invalid UTF-8 strings (minimal example):  
  
```cpp  
  boost::mysql::format_context context({});  
  boost::mysql::format_sql_to(context, "{}", "\x80");  // Invalid UTF-8.  
  boost::system::result<std::string> result = std::move(context).get();  
```  
  
Which isn't valid, and should cause the `result` to have an error. However, that is causing a crash instead:  
  
```cpp  
#0  0x0000000000000000 in ?? ()  
#1  0x0000555555647a24 in boost::mysql::detail::call_next_char (charset=..., first=0x5555559b1565 "\200", last=0x5555559b1566 "")  
    at /usr/local/include/boost/mysql/impl/internal/call_next_char.hpp:32  
#2  0x0000555555647674 in boost::mysql::detail::escape_impl<boost::mysql::detail::quote_escaper> (input=..., charset=..., escaper=...,   
    output=...) at /usr/local/include/boost/mysql/impl/escape_string.ipp:72  
#3  0x00005555556474bc in boost::mysql::detail::escape_string (input=..., opts=..., escape_char=39 '\'', output=...)  
    at /usr/local/include/boost/mysql/impl/escape_string.ipp:128  
#4  0x0000555555646b09 in boost::mysql::detail::append_quoted_string (str=..., ctx=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:107  
#5  0x00005555556464e4 in boost::mysql::detail::append_string (str=..., format_spec=..., ctx=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:124  
#6  0x00005555556460d5 in boost::mysql::detail::append_field_view (fv=..., format_spec=..., allow_specs=true, ctx=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:230  
#7  0x0000555555645f66 in boost::mysql::format_context_base::format_arg (this=0x7fffffffdc58, arg=..., format_spec=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:536  
#8  0x0000555555645e02 in boost::mysql::detail::format_state::do_field (this=0x7fffffffdab8, arg=..., format_spec=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:300  
#9  0x0000555555645d39 in boost::mysql::detail::format_state::do_indexed_field (this=0x7fffffffdab8, arg_id=0, format_spec=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:312  
#10 0x0000555555645386 in boost::mysql::detail::format_state::append_auto_field (this=0x7fffffffdab8, format_spec=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:457  
#11 0x0000555555644f10 in boost::mysql::detail::format_state::parse_field (this=0x7fffffffdab8, it=@0x7fffffffda58: 0x5555559b1564 "",   
    format_end=0x5555559b1564 "") at /usr/local/include/boost/mysql/impl/format_sql.ipp:412  
#12 0x0000555555644a58 in boost::mysql::detail::format_state::format (this=0x7fffffffdab8, format_str=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:493  
#13 0x00005555556445e3 in boost::mysql::detail::vformat_sql_to (ctx=..., format_str=..., args=...)  
    at /usr/local/include/boost/mysql/impl/format_sql.ipp:554  
#14 0x00005555555d7f35 in _ZN5boost5mysql13format_sql_toITpTkNS0_6detail11formattableEJRA2_KcEEEvRNS0_19format_context_baseENS0_20constant_string_viewEDpOT_ (ctx=..., format_str=..., args=...) at /usr/local/include/boost/mysql/format_sql.hpp:495  
#15 0x00005555555d764d in main () at test.cpp:54  
```  
  
Is this crash expected? Or is it because I have provided and 'invalid' format_options? Should the format_sql detect such error?  
  
Thanks in advance!

---

## Comment 1

> Username: anarthal  
> Created at: 2025-01-15 23:08:52 UTC  
> Url: https://github.com/boostorg/mysql/issues/398#issuecomment-2594120431  

Hi! Yes, it's because you specified an invalid format_options. I'd advise to use this for testing:  
  
```cpp  
format_options {utf8mb4_charset, true}  
```  
  
I used to have a page on testing this but I think I removed it. I'll see if I can re-add it.  
  
I should probably have an assert, at least. Most of the use cases, other than testing, should be handled either by using `with_params` or with `any_connection::format_opts`, which don't return invalid format_options, hence I left it as being undefined behaviour. I agree that's not very friendly, though. I'll look into changing it.  
  
Thanks for reporting. If I can help with anything else, let me know.  
  
Ruben.
