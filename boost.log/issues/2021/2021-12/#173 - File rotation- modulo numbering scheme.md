# #173 - File rotation: modulo numbering scheme [Closed]

> Username: ropieur  
> Created at: 2021-12-16 10:42:36 UTC  
> Updated at: 2021-12-16 11:22:26 UTC  
> Closed at: 2021-12-16 11:22:26 UTC  
> Url: https://github.com/boostorg/log/issues/173  

Hi,  
My currently software is rotating with the following scheme with a maximum of 3 backup files  
```  
Previous   ----> Next  
--------------------------  
[rotation] ----> app.log  
app.log    ----> app.log.0  
app.log.0  ----> app.log.1  
app.log.1  ----> app.log.2  
app.log.2  ----> [dropped]  
```  
I call this scheme modulo numbering, because the files count always remains less than the max number of backup files.  
Is it possible to configure the rotation files to obey this scheme?  
If not, I guess that I should create a new backend. Correct? In such event, would it be possible to reuse existing file backend implementation to ease my development efforts?  
Thank you.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-16 11:22:26 UTC  
> Url: https://github.com/boostorg/log/issues/173#issuecomment-995700723  

> Is it possible to configure the rotation files to obey this scheme?  
  
No, the previously rotated files are not renamed by the file collector. This is to avoid the possible failures and avoid O(N) complexity of file rotation.  
  
> If not, I guess that I should create a new backend. Correct?  
  
You could write a new backend (there is a [docs section](https://www.boost.org/doc/libs/1_78_0/libs/log/doc/html/log/extension.html#log.extension.sinks) describing that), but if you just want a different rotated file management then you can reuse the existing `text_file_backend` and only reimplement the file collector. You need to derive from the [`collector`](https://github.com/boostorg/log/blob/a3717b0ccf038724ca4ece79da49844139360bf7/include/boost/log/sinks/text_file_backend.hpp#L74-L129) interface and implement its methods according to the specification. Then you can set your collector to the backend by calling `set_file_collector`.
