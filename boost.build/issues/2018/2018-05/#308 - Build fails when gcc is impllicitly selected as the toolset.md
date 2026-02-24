# #308 - Build fails when gcc is impllicitly selected as the toolset [Closed]

> Username: grisumbras  
> Created at: 2018-05-16 19:35:12 UTC  
> Updated at: 2018-05-22 18:30:24 UTC  
> Closed at: 2018-05-22 18:30:24 UTC  
> Url: https://github.com/boostorg/build/issues/308  

Running plain `b2` fails with this error:  
```  
gcc.compile.c++ bin/gcc/debug/foo.o  
/bin/sh: 2: -fPIC: not found  
  
       -fPIC -O0 -fno-inline -Wall -g     -c -o "bin/gcc/debug/foo.o" "foo.cpp"  
  
...failed gcc.compile.c++ bin/gcc/debug/foo.o...  
```  
  
Running `b2 toolset=gcc` works as expected.  
  
My `user-config.jam` only contains `using gcc ;`  
  
git bisect pointed at commit 15c876025afb3caa2961642179b112f0532dcdf1, and after reverting different changes I found out that rule `feature.add-default` stopped expanding `<toolset>gcc` (it looks like if I manually specify the toolset to use, it gets expanded elsewhere).

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-05-16 20:12:56 UTC  
> Url: https://github.com/boostorg/build/issues/308#issuecomment-389650367  

AMDG  
  
I can't reproduce your problem.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: grisumbras  
> Created at: 2018-05-16 20:29:24 UTC  
> Url: https://github.com/boostorg/build/issues/308#issuecomment-389654964  

Interesting.  
I am using Ubuntu 18.04, no system Boost installed, installed gcc version is 7.3.0.  
  
The whole project is one `jamroot.jam` file with this contents:  
```  
lib foo : foo.cpp ;  
```

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-05-16 20:40:16 UTC  
> Url: https://github.com/boostorg/build/issues/308#issuecomment-389658176  

AMDG  
  
On 05/16/2018 02:29 PM, Dmitry wrote:  
> Interesting.  
> I am using Ubuntu 18.04, no system Boost installed, installed gcc version is 7.3.0.  
>   
> The whole project is one `jamroot.jam` file with this contents:  
> ```  
> lib foo : foo.cpp ;  
> ```   
>   
  
  I was testing at $BOOST_ROOT.  The reason it  
worked for me is that the top level target stage  
target is an alias, so there's an extra expansion  
before it gets to anything that actually needs  
building.  
  
In Christ,  
Steven Watanabe
