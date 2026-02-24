# #14 - Add support for markdown tables [Closed]

> Username: bachase  
> Created at: 2016-12-08 22:05:46 UTC  
> Updated at: 2017-03-28 19:02:00 UTC  
> Closed at: 2017-03-28 19:02:00 UTC  
> Url: https://github.com/boostorg/docca/issues/14  

Doxygen supports markdown style [tables](https://www.stack.nl/~dimitri/doxygen/manual/markdown.html#md_tables), but docca is not transforming them into HTML tables.  
  
For example, updating https://github.com/ripple/rippled/blob/develop/src/ripple/test/BasicNetwork.h#L85-L94 to the markdown style renders as  
![docca](https://cloud.githubusercontent.com/assets/14430/21029568/5c5eb31a-bd68-11e6-8b2e-c6b991e225ea.PNG)  
  
but native doxygen renders as  
  
![doxygen](https://cloud.githubusercontent.com/assets/14430/21029584/7347e81c-bd68-11e6-88aa-82772b349a17.PNG)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-03-28 19:01:49 UTC  
> Url: https://github.com/boostorg/docca/issues/14#issuecomment-289871485  

Oh that looks lovely
