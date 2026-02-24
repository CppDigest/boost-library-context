# #16 - Assertion when iterating over segments() when there are params present [Closed]

> Username: ashley-b  
> Created at: 2020-09-23 09:29:11 UTC  
> Updated at: 2021-09-14 04:50:59 UTC  
> Closed at: 2021-09-14 04:50:58 UTC  
> Url: https://github.com/boostorg/url/issues/16  

When there are params in the url string ie "/path/to/file.txt?w=3"  
the iterator never reaches the end()  
  
Example  
```  
boost::url_view const v{ "/path/to/file.txt?w=3" };  
auto const ps = v.segments();  
std::size_t loop = 1;  
for (auto i = ps.begin(); i != ps.end(); ++i)  
    std::cout << "loop = " << loop << std::endl;  
    std::cout << "s = " << i->string() << std::endl;  
}  
```  
  
Output  
```  
loop = 1  
s = path  
loop = 2  
s = to  
loop = 3  
s = file.txt?w=3  
loop = 4  
s =   
  
url/include/boost/url/impl/url_view.ipp:415: boost::urls::url_view::segments_type::iterator& boost::urls::url_view::segments_type::iterator::operator++(): Assertion `off_ != pt_->offset[ detail::id_frag]' failed.  
Aborted  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-23 16:53:06 UTC  
> Url: https://github.com/boostorg/url/issues/16#issuecomment-697687094  

I'm not working on this library at the moment, and it is unfinished. However, it is next up on my to-do list, after the current formal review of Boost.JSON is finished and the next version of Boost is released. Thanks!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-09-14 04:50:58 UTC  
> Url: https://github.com/boostorg/url/issues/16#issuecomment-918799432  

The read-only views `url_view`, `path_view`, and `query_params_view` should work perfectly now - give it a try!!
