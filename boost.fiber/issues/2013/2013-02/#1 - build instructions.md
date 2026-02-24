# #1 - build instructions [Closed]

> Username: rjpower  
> Created at: 2013-02-18 20:43:52 UTC  
> Updated at: 2013-02-22 15:15:25 UTC  
> Closed at: 2013-02-22 15:15:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/1  

Hey Oliver,  
  
This seems like a really interesting project - I'm keen to try it out, but I'm afraid I'm not very familiar with the boost/jam build system - the obvious commands I've tried out don't seem to work so well.  
  
It might make sense to add a few lines to the README indicating how to run bjam on the project (or a shell script that does the same), for those of us less gifted with boost :)  
  
Thanks!

---

## Comment 1

> Username: olk  
> Created at: 2013-02-21 15:30:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/1#issuecomment-13894574  

Hello Russell,  
  
thanks for your suggestion - the boost libraries in my git repo are adapted to the new boost structure.  
You can find detailed instructions at https://svn.boost.org/trac/boost/wiki/TryModBoost.  
  
git clone http://github.com/boostorg/boost modular-boost  
cd modular-boost  
git submodule update --init  
cd libs  
git clone http://github.com/olk/boost-fiber fiber  
cd ..  
cmake -P forward_headers.cmake  
./bootstrap.sh  
cp b2 /usr/local/bin  
cd libs/fiber/test  
b2  
  
I'll add the description to README.md.  
  
BTW, boost.fiber is not ready yet - unfortunately it contains a raise if a fiber is move to another thread.  
I need more time to find the cause for this raise condition.  
  
best,  
Oliver

---

## Comment 2

> Username: rjpower  
> Created at: 2013-02-22 15:15:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/1#issuecomment-13947787  

Thanks for the quick reply.  For my purposes, I'm okay with having fibers on a single thread.  I'll try it out soon and let you know!
