# #501 - Fix header dependencies in <extension/dynamic_image/dynamic_image_all.hpp> [Closed]

> Username: sdebionne  
> Created at: 2020-06-18 10:02:13 UTC  
> Updated at: 2020-07-26 19:06:00 UTC  
> Closed at: 2020-07-26 19:06:00 UTC  
> Url: https://github.com/boostorg/gil/issues/501  

Following up [discussion on cpplang](https://cpplang.slack.com/archives/CSVT0STV2/p1592473950487700).  
  
Header file `<boost/gil/extension/dynamic_image/dynamic_image_all.hpp>` needs to be refactored to include only what is really needed.  
  
Ideally each headers included in `dynamic_image_all.hpp` should be self contained.  
  
Eventually mark `dynamic_image_all.hpp` as deprecated, individual includes are preferred.
