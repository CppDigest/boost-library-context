# #223 - boost::posix_time::time_from_string not threadsafe until the get_month_map_ptr() has completed once [Open]

> Username: ksankaran2025  
> Created at: 2022-05-27 21:15:22 UTC  
> Updated at: 2024-02-15 06:19:33 UTC  
> Url: https://github.com/boostorg/date_time/issues/223  

The below function which is invoked by time_from_string when a month name such as "May" is used - populates the table when empty. However when two threads make the same call at the same time - there is no synchronization as a result both end up inserting to the same table and causes a crash like this  
   
   #0  0x0000000001015f85 in std::pair<std::_Rb_tree_iterator<std::pair<std::string const, unsigned short> >, bool> std::_Rb_tree<std::string, std::pair<std::string const, unsigned short>, std::_Select1st<std::pair<std::string const, unsigned short> >, std::less<std::string>, std::allocator<std::pair<std::string const, unsigned short> > >::_M_insert_unique<std::pair<std::string, unsigned short> >(std::pair<std::string, unsigned short>&&) ()  
   boostorg/boost#1  0x0000000001013d14 in boost::gregorian::greg_month::get_month_map_ptr() ()  
   boostorg/boost#2  0x00000000006fe148 in boost::date_time::month_str_to_ushort<boost::gregorian::greg_month> (s="May") at /opt/rh/devtoolset-10/root/usr/include/c++/10/ext/new_allocator.h:89  
  boostorg/boost#3  boost::date_time::parse_date<boost::gregorian::date> (s=..., order_spec=<optimized out>) at .../boost/date_time/date_parsing.hpp:142_  
  
  
    greg_month::month_map_ptr_type greg_month::get_month_map_ptr()  
    {  
      static month_map_ptr_type month_map_ptr(new greg_month::month_map_type());  
  
      if(month_map_ptr->empty()) {  
        std::string s("");  
        for(unsigned short i = 1; i <= 12; ++i) {  
          greg_month m(static_cast<month_enum>(i));  
          s = m.as_long_string();  
          s = date_time::convert_to_lower(s);  
          month_map_ptr->insert(std::make_pair(s, i));  
          s = m.as_short_string();  
          s = date_time::convert_to_lower(s);  
          month_map_ptr->insert(std::make_pair(s, i));  
        }  
      }  
      return month_map_ptr;  
  }

---

## Comment 1

> Username: phetdam  
> Created at: 2024-02-15 06:19:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/223#issuecomment-1945438667  

Noticed the same issue when calling `boost::posix_time::time_from_string` from multiple threads.  
  
Can at least the [documentation](https://www.boost.org/doc/libs/1_84_0/doc/html/date_time/posix_time.html#:~:text=ptime%20time_from_string(std%3A%3Astring)) be updated to indicate that the function is not thread-safe? It would be nice (but not necessary) if there was a thread-safe variant, e.g. `boost::posix_time::time_from_string_reentrant`.  
  
I suppose the implementation could be as simple as using a static mutex to serialize calls to `time_from_string`.
