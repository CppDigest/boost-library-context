# #26 - Consider renaming hana::demux and hana::lockstep [Closed]

> Username: ldionne  
> Created at: 2015-03-22 16:46:40 UTC  
> Updated at: 2015-08-23 17:48:15 UTC  
> Closed at: 2015-08-23 17:48:15 UTC  
> Url: https://github.com/boostorg/hana/issues/26  

I have never been really fond of those, but I could never think of something better. If someone can come up with something that's better, I'll be happy to change it. To understand what those guys do, see [here](https://github.com/ldionne/hana/blob/6c90eb30beff2e947e311b0b0472097d753d663c/include/boost/hana/functional/demux.hpp#L20) for `demux` and [here](https://github.com/ldionne/hana/blob/6c90eb30beff2e947e311b0b0472097d753d663c/include/boost/hana/functional/lockstep.hpp#L20) for `lockstep`. Basically, they are two different ways to compose functions, along with `compose`.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-08-23 17:48:15 UTC  
> Url: https://github.com/boostorg/hana/issues/26#issuecomment-133882087  

The names are not great, but at the same time there's no compelling reason to change them. I will wait for someone to either complain about the names or to suggest something better before sweating over this. Hence, closing.
