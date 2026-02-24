# #154 - Unable to track pointer instances correctly [Closed]

> Username: doliwa  
> Created at: 2019-04-16 23:38:38 UTC  
> Updated at: 2019-10-20 21:39:27 UTC  
> Closed at: 2019-10-20 21:39:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/154  

The following simple example shows a bug in de-serializing a vector of a simple struct, involving a mixture of pointers and strings.  
  
I attach the cpp-reproducer as a txt file because the code insertion seems to be broken here...  
  
[reproducer1.cpp.txt](https://github.com/boostorg/serialization/files/3087333/reproducer1.cpp.txt)

---

## Comment 1

> Username: robertramey  
> Created at: 2019-04-17 16:37:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/154#issuecomment-484167386  

just took a short look at this before I really get serious.  
  
Which test macro test fails.  Is it CHECK_EQUAL( ms[0].t_pointer->x, "tracked" ); ?  I ask because it looks suspicious to me.  
  
it expands to:  
  
assert(X==Y)  
assert(ms[0].t_pointer->x, "tracked");   
  which compares a string to a pointer to a const address of characters.  So this depends upon the == operator implicitly converting the "tracked" to a string which I don't believe will happen.  Perhaps you might try:  
  
assert(ms[0].t_pointer->x, std::string("tracked"));   
  
instead and see how that works

---

## Comment 2

> Username: doliwa  
> Created at: 2019-04-23 09:26:01 UTC  
> Updated at: 2019-04-23 09:26:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/154#issuecomment-485724712  

The message from the executable is:  
reproducer1.cpp:63: int main(int, char**): Assertion `ms[0].t_pointer==ms[1].t_pointer' failed.

---

## Comment 3

> Username: robertramey  
> Created at: 2019-04-23 16:10:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/154#issuecomment-485870811  

same issue.    
a) you serialize two instances of the structure Main  
b) you expect that the the value of x should point the memory location.  I would not expect that  
c) when you recover the pointers they are different.  This is what I expect.  
  
To check if I'm right update the code in the following way:  
  
`    {  
        Mains ms;  
        ms.push_back( Main());  
        ms.push_back( Main());  
        ms.back().t_pointer = ms.front().t_pointer = new Tracked;  
  
        REQUIRE_EQUAL( ms.size(), 2);  
        CHECK_EQUAL( ms[0].t_pointer, ms[1].t_pointer );  
        CHECK_EQUAL( ms[0].t_pointer->x, "tracked" );  
  
        boost::archive::text_oarchive oa(ss);  
        oa << ms;  
          
        delete ms.back().t_pointer;  
    }  
`

---

## Comment 4

> Username: doliwa  
> Created at: 2019-04-23 21:27:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/154#issuecomment-485981345  

Robert, thanks for your comments. Although I don't get your point, I did as you have proposed,  
see   
[reproducer2.cpp.txt](https://github.com/boostorg/serialization/files/3109668/reproducer2.cpp.txt)  
  
Again, I get:  
reproducer2.cpp:67: int main(int, char**): Assertion `ms[0].t_pointer==ms[1].t_pointer' failed.  
  
i.e., problems occur only after de-serialization.

---

## Comment 5

> Username: robertramey  
> Created at: 2019-10-20 21:39:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/154#issuecomment-544295480  

OK - very good test.  It isolated the first real bug found in years.  Very subtle issue related to serialization through.  Given the test case, I couldn't let it go and after spending a large part of the weekend managed to fix it.  It passes your test while still passing all the standard library tests.  So I'm pretty confident that this is a definitive fix.  Ever the optimist, I'm closing this Issue.
