# #191 avoided misleading indentation warning [Merged]

> Username: joaquintides  
> Created at: 2020-02-24 08:28:41 UTC  
> Updated at: 2020-03-04 08:19:41 UTC  
> Merged at: 2020-03-04 04:17:00 UTC  
> Closed at: 2020-03-04 04:17:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/191  

Clang 10.0 says:  
  
`../boost/archive/basic_text_oprimitive.hpp:169:13: warning: misleading indentation;  
statement is not part of the previous 'if' [-Wmisleading-indentation]`  
  
(see https://www.boost.org/development/tests/develop/output/teeks99-dkr-dc10-2a-multi_index-clang-linux-10~c++2a-warnings.html#test_serialization)

---

## Comment 1

> Username: robertramey  
> Created_at: 2020-02-24 15:23:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-590376787  

Hmmm - I see a couple of issues here.  
  
a) the indentation.  I personally don't see an issue with it.  I often indent the code conditional on an #if just as I do with with the if(..) statement.  I've bad experience trying to accommodate the warnings policy of various compilers.  They conflict with each other in general and not everyone uses the same warning switches.  
  
b) The insertion of +2 is a substantive change.  What is the motivation and reasoning behind this?  If it's legitimate, I would add a comment explaining why it's there.  
  
Please think about this some more.

---

## Comment 2

> Username: joaquintides  
> Created_at: 2020-02-24 15:57:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-590400109  

a) I don't have any issue with the indentation, either, it's just Clang that happens to warn about it. It's of course your call to decide how to act upon this.  
b) I haven't inserted any `+2`, it was there in the original code.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2020-02-26 19:25:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-591599527  

Hi,  
  
@robertramey I understand this warning may sound non legit to some. gcc which has the same warning does't yield anything here. Basically clang tries to tell you that, without the preprocessing things, you code looks like this:  
  
```  
if(os.fail())  
    boost::serialization::throw_exception(...);  
    const unsigned int digits = std::numeric_limits<T>::max_digits10;  
```  
  
and tries to warn you that you indented the code as if these two statements were a single block, while in effect the compiler will consider only the first statement to be part of the "if" block."  
  
You are definitely in a corner case of such a warning, and to be honest, I doubt there is any good or bad decision the compiler can make here.  
  
So, I see three ways to fix this:  
 1) The approach used here.  
 2) Adding braces {} around the throw_exception in the "if".  
 3) Or decide that you want to ignore this warning via pragma diagnostics.  
  
In all cases, some people (ie I am part of them) consider this warning more useful than annoying, it did find quite some real bugs in our company code, so we definitely wish to use it, and be able to include Boost headers ;)  
  
Cheers,  
Romain

---

## Comment 4

> Username: robertramey  
> Created_at: 2020-02-27 05:11:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-591784882  

>here. Basically clang tries to tell you that, without the preprocessing things, you code looks like >this:  
  
>if(os.fail())  
>    boost::serialization::throw_exception(...);  
>const unsigned int digits = std::numeric_limits<T>::max_digits10;  
  
Right - that is my intent.   
  
It's the preprocessor - I don't see {} as being applicable here.

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created_at: 2020-02-27 08:06:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-591836781  

You mean you won't accept either a change that would result in adding braces like this ?  
  
```  
    if(os.fail())  
    {  
        boost::serialization::throw_exception(...);  
    }  
```  
  
Did I understand correctly ? If yes, then let's just add a pragma to ignore this warning then.

---

## Comment 6

> Username: robertramey  
> Created_at: 2020-02-27 15:22:37 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-592018494  

I don't see how one could add {} to a preprocessor #if/#else/#endif.  
  
Feel free to suggest a pragma - but that will likely generate another warning on some platforms.  FYI - most gcc/clang warnings are suppressible via command line switches - these can be included with b2 - which is what I do.  But of course note everyone uses b2.  
  
Also I haven't looked into whether this warning comes up for all library users or just when the library is built.  In the latter case, it would be less of an annoyance.  
  
I've been down this road before. It usually ends up as a game of whack-a-mole while trying to compile with variations of different compilers.

---

## Comment 7

> Username: alfC  
> Created_at: 2020-02-27 15:32:37 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-592024414  

I think that what Joaquin says is that the code could be modified into one the following options. None is ideal but all would solve the warning and are harmless. I removed the ext comments for simplicity:  
  
option 1:  
```c++  
    template<class T>  
    void save_impl(const T &t, boost::mpl::bool_<true> &){  
        if(os.fail()){  
            boost::serialization::throw_exception(  
                archive_exception(archive_exception::output_stream_error)  
            );  
        }  
        #ifndef BOOST_NO_CXX11_NUMERIC_LIMITS  
            const unsigned int digits = std::numeric_limits<T>::max_digits10;  
        #else  
            const unsigned int digits = std::numeric_limits<T>::digits10 + 2;  
        #endif  
        os << std::setprecision(digits) << std::scientific << t;  
    }  
```  
  
option 2:  
```c++  
    template<class T>  
    void save_impl(const T &t, boost::mpl::bool_<true> &){  
        if(os.fail()) boost::serialization::throw_exception(  
                archive_exception(archive_exception::output_stream_error)  
        );  
        #ifndef BOOST_NO_CXX11_NUMERIC_LIMITS  
            const unsigned int digits = std::numeric_limits<T>::max_digits10;  
        #else  
            const unsigned int digits = std::numeric_limits<T>::digits10 + 2;  
        #endif  
        os << std::setprecision(digits) << std::scientific << t;  
    }  
```  
  
option 3:  
  
```c++  
    template<class T>  
    void save_impl(const T &t, boost::mpl::bool_<true> &){  
        if(os.fail())  
            boost::serialization::throw_exception(  
                archive_exception(archive_exception::output_stream_error)  
            );  
        #ifndef BOOST_NO_CXX11_NUMERIC_LIMITS  
        const unsigned int digits = std::numeric_limits<T>::max_digits10;  
        #else  
        const unsigned int digits = std::numeric_limits<T>::digits10 + 2;  
        #endif  
        os << std::setprecision(digits) << std::scientific << t;  
    }  
```  
  
option 4:  
  
```c++  
    template<class T>  
    void save_impl(const T &t, boost::mpl::bool_<true> &){  
        if(os.fail())  
            boost::serialization::throw_exception(  
                archive_exception(archive_exception::output_stream_error)  
            );  
        const unsigned int digits =   
            #ifndef BOOST_NO_CXX11_NUMERIC_LIMITS  
                std::numeric_limits<T>::max_digits10  
            #else  
                 std::numeric_limits<T>::digits10 + 2  
            #endif  
        ;  
        os << std::setprecision(digits) << std::scientific << t;  
    }  
```  
  
There are of course many other options that will equally silence the "misleading indentation" warning.

---

## Comment 8

> Username: robertramey  
> Created_at: 2020-02-28 06:02:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-592328858  

I don't see any difference between options 1), 2), 4) and the original code which provoked the warning.  
  
I don't see any difference between option 3) and joaquin's solution

---

## Comment 9

> Username: joaquintides  
> Created_at: 2020-02-28 08:25:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-592404783  

> Also I haven't looked into whether this warning comes up for all library users or just when the library is built. In the latter case, it would be less of an annoyance.  
  
It is the former case, warning comes up when including `<boost/archive/text_oarchive.hpp>` in user code.  
  
For the sake of curiosity, I've looked for other examples of macro-guarded code in Boost.Serialization, and indentation is not always used:  
  
https://github.com/boostorg/serialization/blob/ece3187e2498cbfd0d1acf98ee9ac4bb7c2ca1e5/include/boost/archive/polymorphic_iarchive.hpp#L64-L77

---

## Comment 10

> Username: alfC  
> Created_at: 2020-02-28 16:35:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-592592863  

On Thu, Feb 27, 2020 at 22:02 Robert Ramey <notifications@github.com> wrote:  
  
> I don't see any difference between options 1), 2), 4) and the original  
> code which provoked the warning.  
>  
  
All the options are semantically identical.  The difference is that they  
will not produce the indentation warning.  
  
> I don't see any difference between option 3) and joaquin's solution  
>  
  
The idea is that you have more choices, perhaps giving one more consistent  
with your indentation practices.  
  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/pull/191?email_source=notifications&email_token=AARR6YDCTSUL5OYSBNWOZCLRFCSFTA5CNFSM4K2DRNOKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOENHDRGQ#issuecomment-592328858>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AARR6YATZCILY3Q4WTX4GDTRFCSFTANCNFSM4K2DRNOA>  
> .  
>

---

## Comment 11

> Username: robertramey  
> Created_at: 2020-02-28 16:57:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-592603847  

>All the options are semantically identical.  The difference is that they  
will not produce the indentation warning.  
  
Hmmm - I thought the "problem" was the indentation that this produced the warning on clang and gcc.  So why does the current code produce this warning? It looks like the same code to me?

---

## Comment 12

> Username: joaquintides  
> Created_at: 2020-03-02 08:37:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-593284076  

> So why does the current code produce this warning? It looks like the same code to me?  
  
All variations proposed by @alfC are exactly the same in terms of the code they produce, they only differ in indentation and/or spacing from current code. Current code looks like this _after macro expansion_, if we remove comments for clarity:  
```  
    template<class T>  
    void save_impl(const T &t, boost::mpl::bool_<true> &){  
        if(os.fail())  
            boost::serialization::throw_exception(  
                archive_exception(archive_exception::output_stream_error)  
            );  
            const unsigned int digits = std::numeric_limits<T>::max_digits10;  
     os << std::setprecision(digits) << std::scientific << t;  
    }  
```  
Clang is warning that the `const unsigned int digits =...` line is indented as if the author meant it to be part of the `if(os.fail()` branch —which is not.

---

## Comment 13

> Username: robertramey  
> Created_at: 2020-03-03 02:04:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-593727668  

option 1) works for me.  Make the change and I'll accept the PR

---

## Comment 14

> Username: joaquintides  
> Created_at: 2020-03-03 12:22:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/191#issuecomment-593923671  

Done.

---
