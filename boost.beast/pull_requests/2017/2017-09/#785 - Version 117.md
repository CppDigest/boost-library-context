# #785 Version 117 [Merged]

> Username: vinniefalco  
> Created at: 2017-09-20 03:10:01 UTC  
> Updated at: 2017-10-01 17:07:04 UTC  
> Merged at: 2017-09-20 18:13:30 UTC  
> Closed at: 2017-09-20 18:13:30 UTC  
> Url: https://github.com/boostorg/beast/pull/785  

* Add buffers_to_string  
  
API Changes:  
  
* buffers_suffix replaces consuming_buffers  
* buffers_prefix replaces buffer_prefix  
* buffers_prefix_view replaces buffer_prefix_view  
* buffers_front replaces buffer_front  
* buffers_cat replaces buffer_cat  
* buffers_cat_view replaces buffer_cat_view  
* Remove buffers_suffix::get  
  
Actions Required:  
  
* Use buffers_suffix instead of consuming_buffers  
* Use buffers_prefix instead of buffer_prefix  
* Use buffers_prefix_view instead of buffer_prefix_view  
* Use buffers_front instead of buffer_front  
* Use buffers_cat instead of buffer_cat  
* Use buffers_cat_view instead of buffer_cat_view  
* Avoid calling buffers_suffix::get

---
