# #200 - Where is file boost/project-config.jam? [Closed]

> Username: tiendq  
> Created at: 2018-10-04 04:18:04 UTC  
> Updated at: 2018-11-27 00:30:56 UTC  
> Closed at: 2018-11-27 00:30:56 UTC  
> Url: https://github.com/boostorg/boost/issues/200  

This [cheatsheet](https://github.com/boostorg/boost/wiki/Getting-Started-Cheat-Sheet) mentions about file `project-config.jam` in setup steps but I didn't find this file.  
  
> In project-config.jam, set the libraries = line to read libraries = --with-date_time ;

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-10-04 04:31:36 UTC  
> Url: https://github.com/boostorg/boost/issues/200#issuecomment-426882011  

AMDG  
  
On 10/03/2018 10:18 PM, Tien Do wrote:  
> This [cheatsheet](https://github.com/boostorg/boost/wiki/Getting-Started-Cheat-Sheet) mentions about file `project-config.jam` in setup steps but I didn't find this file.  
>   
  
It's created by booststrap.sh.  The steps  
shown are in the wrong order.  
  
>> In project-config.jam, set the libraries = line to read libraries = --with-date_time ;  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: tiendq  
> Created at: 2018-10-04 06:03:17 UTC  
> Url: https://github.com/boostorg/boost/issues/200#issuecomment-426895509  

Thanks, it works now. I have also updated the cheatsheet steps with correct order.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-27 00:26:03 UTC  
> Url: https://github.com/boostorg/boost/issues/200#issuecomment-441851370  

@tiendq if your issue is resolved, please close this.
