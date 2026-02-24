# #1116 - Question about boost.json and unescaped control characters [Closed]

> Username: freak82  
> Created at: 2025-10-16 11:31:27 UTC  
> Updated at: 2025-10-16 12:27:48 UTC  
> Closed at: 2025-10-16 12:27:48 UTC  
> Url: https://github.com/boostorg/json/issues/1116  

Hi there,  
  
Is it possible to "make" the boost.json parser allow unescaped control characters in JSON string values?  
It seems to me that `allow_invalid_utf8` and `allow_invalid_utf16` `parse_options` doesn't help with this.   
I also looked at the source code of `parse_string` function and it seems to me that even if I provide a handler with `on_string` member function the parser will still throw an error because of the unescaped control characters before calling `on_string`.  
Maybe my only option is to escape such control characters before giving the JSON data to the boost json parser?  
  
Here is a code snipped which shows the problem. The JSON data/string is from the logs in our production environment. The exact problem in this particular case is that the username contains CR LF bytes in front. `bjson` is an alias for `boost::json`.  
  
```cpp  
    // clang-format off                                                           
    std::string_view data =                                                       
        R"({"event":"session-pre-finished","ctrl_type":"pppoe","channel_name":"3c:d9:2b:53:e6:96","session_id":"000000008e3525c9","called_station_id":"3c:fd:fe:c0:b9:e0","calling_station_id":"3c:d9:2b:53:e6:96","name":"pppoe","username":"  
        k028-gfazal4","ip_addr":"","ip6_addr":"","ip6dp_addr":"","pppoe_sessionid":12737,"ctrl_ifname":"vlan603","nas_identifier":"pace-bngs-02","service_name":""})";  
    // clang-format on                                                            
    try {                                                                         
        unsigned char tmp_mem[4096];                                              
        bjson::parse_options opts = {};                                           
        opts.allow_invalid_utf8   = true;                                         
        opts.allow_invalid_utf16  = true;                                         
        bjson::parser parser(bjson::storage_ptr(), opts, tmp_mem);                
        parser.write(data);                                   
    } catch (const std::exception& ex) {                                          
        std::println("Got exception: {}", ex.what());              
    }   
```  
And this code prints  
```  
Got exception: syntax error [boost.json:1 at /z/x3me_libs/boost_1_87_0/boost/json/basic_parser_impl.hpp:1084:54 in function 'const char* boost::json::basic_parser<Handler>::parse_string(const char*, std::integral_constant<bool, StackEmpty_>, std::integral_constant<bool, AllowComments_>, bool, bool)']  
```  
  
Thanks,  
Pavel.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-10-16 11:51:33 UTC  
> Url: https://github.com/boostorg/json/issues/1116#issuecomment-3410496725  

Right. Indeed, the JSON grammar doesn't allow unescaped ASCII control characters in strings. Unfortunately, I don't think we will add an extension that would allow them, unless there's large support for it. `allow_invalid_utf8` was added because of a similar feature in inRrapidJSON, and because it can increase performance. `allow_invalid_utf16` was added because it supports so called WTF-8. Given that this has a name, I considered it a popular enough use case.  
  
Allowing unescaped ASCII control characters would not add anything in terms of performance, unless we also disable escapes. So the only way to convince me to support this is to show a big community demand for this.  
  
For now, I can only suggest you escape control characters when you generate the input. And if you don't control input generation, postprocess it.  
  
BTW, what happens if a username contains a quote character?

---

## Comment 2

> Username: freak82  
> Created at: 2025-10-16 12:26:12 UTC  
> Updated at: 2025-10-16 12:26:24 UTC  
> Url: https://github.com/boostorg/json/issues/1116#issuecomment-3410660444  

I'm not arguing for adding support for non-escaped control characters. I understand that we have pretty edge case in our environment.  
I was just trying to confirm my suspicion that there is no such support currently that I'm somehow missing.  
We don't control the input generation. Our application receives these JSONs over the network from a 3rd party application.   
We'll need to preprocess the data to escape the possible control characters.  
  
> BTW, what happens if a username contains a quote character?  
  
I'm not sure. We can't easily test that because we don't control the application generating these JSONs.  
However, I expect that we'll get broken JSON data, with a quote in the middle of the username, in this case too.  
  
Should I close this issue or you'll close it?

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-10-16 12:27:48 UTC  
> Url: https://github.com/boostorg/json/issues/1116#issuecomment-3410666525  

I'll close it. still thank you for the issue, as it can be now linked to in future discussions.
