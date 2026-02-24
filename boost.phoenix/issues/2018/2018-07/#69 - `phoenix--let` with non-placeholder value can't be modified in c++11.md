# #69 - `phoenix::let` with non-placeholder value can't be modified in c++11 [Open]

> Username: Flast  
> Created at: 2018-07-12 02:54:29 UTC  
> Updated at: 2018-11-04 19:27:21 UTC  
> Url: https://github.com/boostorg/phoenix/issues/69  

Following code doesn't compile in C++11, but C++03 does.  
  
```cpp  
#include <boost/phoenix/core.hpp>  
#include <boost/phoenix/operator/self.hpp>  
#include <boost/phoenix/operator/arithmetic.hpp>  
#include <boost/phoenix/scope/let.hpp>  
#include <boost/phoenix/scope/local_variable.hpp>  
using namespace boost::phoenix;  
using namespace local_names;  
  
int main()  
{  
    let(_a = 0)  
    [  
        --_a  
    ]();  
}  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-23 02:35:16 UTC  
> Url: https://github.com/boostorg/phoenix/issues/69#issuecomment-432065614  

This is https://svn.boost.org/trac10/ticket/8298.  
  
@fletchjp said there:  
> The bug here is that this ever worked. The local variables _a do not provide a true variable. This means that _a = 17 followed by _a = 18 amounts to 17 = 18 i.e. assigning to a constant.

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-11-04 18:44:51 UTC  
> Updated at: 2018-11-04 19:27:21 UTC  
> Url: https://github.com/boostorg/phoenix/issues/69#issuecomment-435694950  

I investigated this and found multiple bugs.  
  
  1. The [`let` documentation](https://www.boost.org/doc/libs/1_68_0/libs/phoenix/doc/html/phoenix/modules/scope/let.html) states that `_a` should has `int` type, but for C++11 it is `int const&` because of tr1/decltype result_of differences https://wandbox.org/permlink/27AcBJnXskKPl9uU. If you define `BOOST_RESULT_OF_USE_TR1` the example will compile run https://wandbox.org/permlink/Daf4VxdLyETDRs8p.  
  
  2. Internally Phoenix places locals into a vector and than operates on it through const reference, so actually it cannot modify it and passes `_a` as `int const&`, but mutual operators were working because `int const&` was casted to `int&` here https://github.com/boostorg/phoenix/blob/5f2061241f528123cc04740294e4811a79138f3a/include/boost/phoenix/scope/detail/local_variable.hpp#L203  
  
  3. The other bug is that `let(_a = 0)[_a]()` returns a reference to temporary (with `BOOST_RESULT_OF_USE_TR1` even non-const!).  
  
It looks like a disaster, users can use those references to temporaries and do not know this. I have some thoughts about this:  
  
  1. I think that `let(_a = 0)[--_a]()` should work.  
  2. The return type of it (and `let(_a = 0)[_a]()`) must be a value.  
  3. Making things above will require to distinguish between temporaries and references. With C++11 it is not a problem (rvalues will be stored as value types), but for C++98 `let` will start copying const references values (it can be avoided by users with `ref`; non-const values will work fine).

---

## Comment 3

> Username: fletchjp  
> Created at: 2018-11-04 19:20:06 UTC  
> Url: https://github.com/boostorg/phoenix/issues/69#issuecomment-435697520  

Nikita  
  
Thank you for tackling this problem.  You will see further back down this thread that I wrote before.  Some years ago for some time I was attempting to sort out the problems in Phoenix and I did some but not others.  After a while I handed the task to others.  I am still around and if you find my name in things do ask me what I was doing.  
  
I made some comments on the error handling systems.  I never had the power to change the status so some things may not have been documented as closed although I did.  Some of my work is documented in a ChangeLog in Phoenix.  I don't think it has been used since my efforts.  
  
Best wishes  
  
John Fletcher  
  
________________________________  
From: Nikita Kniazev [notifications@github.com]  
Sent: 04 November 2018 18:44  
To: boostorg/phoenix  
Cc: Fletcher, John P; Mention  
Subject: Re: [boostorg/phoenix] `phoenix::let` with non-placeholder value can't be modified in c++11 (#69)  
  
  
I investigated this and found multiple bugs.  
  
  1.  The let documentation<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.boost.org%2Fdoc%2Flibs%2F1_68_0%2Flibs%2Fphoenix%2Fdoc%2Fhtml%2Fphoenix%2Fmodules%2Fscope%2Flet.html&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7Cc5aa6ca65f5e48e3a85808d642859b9f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636769538939201706&sdata=9wqFdRWLZvQqGJP%2Bp61BL0fXNV7L%2FbguILk4FojyWv4%3D&reserved=0> states that _a should has int type, but for C++11 it is int const& because of tr1/decltype result_of differences https://wandbox.org/permlink/27AcBJnXskKPl9uU<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwandbox.org%2Fpermlink%2F27AcBJnXskKPl9uU&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7Cc5aa6ca65f5e48e3a85808d642859b9f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636769538939201706&sdata=bo2FgVnR4OBpSUJH3yF7q2X%2B%2FjyDBI4sRH%2B%2BlbI%2B8j8%3D&reserved=0>. If you define BOOST_RESULT_OF_USE_TR1 the example will compile run https://wandbox.org/permlink/Daf4VxdLyETDRs8p<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwandbox.org%2Fpermlink%2FDaf4VxdLyETDRs8p&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7Cc5aa6ca65f5e48e3a85808d642859b9f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636769538939211711&sdata=slwkXqqz9Gkz4fsim0KEa8Zfg03jvLiZrf5VDwbQB5A%3D&reserved=0>.  
  
  2.  Internally Phoenix places locals into a vector and than operates on it through const reference, so actually it cannot modify it and passes _a as int const&, but mutual operators were working because int const& was casted to int& here buthttps://github.com/boostorg/phoenix/blob/5f2061241f528123cc04740294e4811a79138f3a/include/boost/phoenix/scope/detail/local_variable.hpp#L203  
  
  3.  The other bug that let(_a = 0)[_a]() returns a reference to temporary (with BOOST_RESULT_OF_USE_TR1 even non-const!).  
  
It looks like a disaster, users can use those references to temporaries and do not know this. I have some thoughts about this:  
  
  1.  I think that let(_a = 0)[--_a]() should work.  
  2.  The return type of it (and let(_a = 0)[_a]()) must be a value.  
  3.  Making things above will require to distinguish between temporaries and references. With C++11 it not be a problem (rvalues will be stored as value types), but for C++98 let will start copying const references values (it can be avoided by users with ref; non-const values will work fine).  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fissues%2F69%23issuecomment-435694950&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7Cc5aa6ca65f5e48e3a85808d642859b9f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636769538939221721&sdata=OW8bSrxtD3NijxeTs4poIJaI4SvlTsbtJ58kJrf48KA%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG2344Q_BfZ5Loe4mJ1vbQHhnA3Wwiiks5urzWjgaJpZM4VMIV6&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7Cc5aa6ca65f5e48e3a85808d642859b9f%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636769538939221721&sdata=94foUisKpwUeHUFbw%2Bu5b2DHMG94HYOHfPE26HmFwSI%3D&reserved=0>.
