# #248 - magnet link example [Closed]

> Username: vinniefalco  
> Created at: 2022-07-27 14:11:10 UTC  
> Updated at: 2022-08-02 04:14:35 UTC  
> Closed at: 2022-08-02 04:14:35 UTC  
> Url: https://github.com/boostorg/url/issues/248  

We need an example that shows how to use custom parsing to process alternate URI schemes, in this case "magnet"  
  
https://www.howtogeek.com/764859/what-is-a-magnet-link-and-how-do-you-use-one/  
  
https://en.wikipedia.org/wiki/Magnet_URI_scheme  
  
Synopsis:  
  
```  
struct magnet_link_view  
{  
    url_view u; // the underlying URL  
  
    // more stuff as needed  
};  
  
struct magnet_link_rule_t  
{  
    using value_type = magnet_link_view;  
      
    result< value_type >  
    parse( char const*& it, char const* end ) const noexcept;  
      
    // whatever options are needed (which could be nothing)  
};  
  
constexpr magnet_link_rule_t magnet_link_rule_t{};  
  
// user-facing function  
/** Return a parsed magnet link from a string, or error.  
*/  
result< magnet_link_view >  
parse_magnet_link( string_view s ) noexcept;  
```
