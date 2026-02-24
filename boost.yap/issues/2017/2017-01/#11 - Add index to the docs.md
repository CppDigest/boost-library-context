# #11 - Add index to the docs [Closed]

> Username: tzlaine  
> Created at: 2017-01-13 17:38:16 UTC  
> Updated at: 2018-06-11 17:16:17 UTC  
> Closed at: 2017-01-14 20:33:01 UTC  
> Url: https://github.com/boostorg/yap/issues/11  

Via the Boost, list, Paul Bristow says:  
  
RTFM ;-)  
  
http://www.boost.org/doc/libs/1_63_0/tools/auto_index/doc/html/index.html  
  
You can just get indexes of macro, class, functions, and by adding index terms to your .idx file, you can get a subject index too.  
  
To see the autoindex in (big!) action, look at Boost.Math docs.  
  
Attached example may help too  
  
modular-boost\libs\circular_buffer\doc\jamfile.v2 may help is making the admittedly non-trivial  changes to get a full index.  
  
You need some items in your .qbk file too (to say which indexes you want).  
  
[/Include the indexes (class, function and everything) ]  
'''  
   <index type="class_name">  
     <title>Class Index</title>  
   </index>  
  
    <index type="function_name">  
     <title>Function Index</title>  
    </index>  
  
   <index/>  
  
'''
