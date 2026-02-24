# #220 - malformed XML Archive causes abort() [Open]

> Username: hajokirchhoff  
> Created at: 2020-09-10 08:40:27 UTC  
> Updated at: 2022-11-15 18:14:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/220  

I have an XML Archive where the past four bytes have been truncated by some unknown reason. When a user tries to load this file, the application is aborted without any further message.  
  
The problem is: the xml_iarchive destructor calls a function that throws an exception and the destructor does not catch this exception.  Throwing an exception inside a destructor is forbidden in C++ and causes the runtime to terminate()  
  
IOW: Simply loading a particular (malformed) file will cause the entire application to immediately terminate, without any message, without any chance for the programmer to catch an exception.  
  
  
  
  
  
This is definitely a bug: xml_iarchive cannot read malformed archives. That is no problem by itself. But throwing an exception inside a destructor will abort() the program. That is standard C++ behaviour: It is never allowed to throw inside a destructor.  
  
So the bug is this: When xml_iarchive tries to read a malformed archive, the application will abort() - close itself without any further message or chance by the program to do something about it.  
  
This manifests itself as follows: any application works fine until a user selects a defective file, where the last few bytes have been truncated or clobbered. This causes the application to terminate, loosing any work the user might have done.  
  
The correct way should be to have at least a try catch statement inside the destructor.  
```  
try {  
    if(0 == (this->get_flags() & no_header)){  
        gimpl->windup(is);  
    }  
}  
catch (...) {  
// This is too bad, but there is nothing we can do about a malformed closing tag.  
// Still it is much better to silently discard the error than terminating the application.  
}  
```  
  
_Originally posted by @hajokirchhoff in https://github.com/boostorg/serialization/issues/82#issuecomment-690083887_

---

## Comment 1

> Username: correaa  
> Created at: 2020-09-10 12:39:44 UTC  
> Updated at: 2020-09-10 13:02:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-690254414  

(If I am not mistaken you can throw an exception from a destructor.  Perhaps you cannot in this case because it is declared (implicitly?) `noexcept` in some base of the class.)  
  
In any case, whether this is technically correct or not, I think a serialization archive (i.e. Robert) is entitled to terminate the program if there was a *stream* error which ultimately it is consistency error.  
The point is that the archive object doesn't need to be responsible of that.  
  
This may leave a bad taste, as if there is nothing it can be done about this particular case.  
Actually, you can do something that doesn't involve the archive type thanks to the fact that there is another object on the fly that *could* (if you want) make responsible of some subset of errors (I think including yours), that is the `std::stream` object.   
  
Stream objects can be optionally set up to throw exceptions when setting fail bits.  
  
https://en.cppreference.com/w/cpp/io/basic_ios/exceptions  
http://www.cplusplus.com/reference/ios/ios/exceptions/  
  
```  
        std::ifstream in("in.xml");  
        in.exceptions(std::ifstream::failbit | std::ifstream::badbit | std::ifstream::eofbit );  
        try{  
            boost::archive::xml_iarchive xia{in}; // seems to be a good scope for xia?  
            ... use xia  
        }catch( std::ifstream::failure e  ){ // or catch(...)???  
           // not much you can do here, BUT you can try, for example, 1) reset the deserialized object to some valid or default state. 2) print a useful message, print the line, position or content in which the file failed, 3) rethrow  
        }  
```  
  
I mentioned this to Robert, in principle the archive could stop trying to handle any stream error by exploiting this feature of standard streams.  
  
Please try this an let us know how it goes.

---

## Comment 2

> Username: hajokirchhoff  
> Created at: 2020-09-10 14:02:02 UTC  
> Updated at: 2020-09-10 14:07:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-690310068  

Hi, sorry for my long answer. The bug is a bit tricky to understand.  
  
Your suggestion "streams can be set up to throw exceptions when settings fail bits" and a try-catch block will not work. Here is why:  
  
Consider this damaged serialization file. Everything is fine except for the final closing bracket, which is missing. Somehow the file got damaged.  
```  
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="18">  
    <header class_id="0" tracking_level="0" version="1">  
    </header>  
</boost_serialization  
```  
  
The ~xml_iarchive destructor calls gimpl->windup(is), which will read characters from the stream until it encounters a closing bracket. But it does not matter if the stream object throws an exception, or if the windup throws the exception itself, when it tries reading past eof. (basic_xml_grammar::my_parse, line 194 throws an exception when is.fail() is true. It basically does the same thing than what you suggest doing with the stream object).  
  
The bug in the library is that the destructor calls a method (windup) that may throw an exception. That should almost never be done.  
  
Destructors are declared noexcept by default and have been since 1990something. I am pretty sure throwing from a destructor has been forbidden since C++ 1.0 (although there may be some recent developments that I am not aware of).   
  
https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Re-never-fail  
  
BTW, the reason is simple: An exception causes a stack to unwind, calling all destructors. But if the destructor of an object fails, what should the program do?  
  
And no, a serialization archive is _definitely not_ entitled to terminate the program. No library is - and should ever be.   
  
Think about it: You are typing a text in a text editor. You spent an hour writing a complex text and then want to insert a "thingamajig" into the text. This "thingamajig" was previously serialized but the file got damaged somehow. Perhaps the computer shut down before finishing the file. Perhaps a virus overwrote the last n bytes. The </boost_serialization> tag has been damaged, the application simply terminates and your hour worth of typing vanishes.  
  
The reason is that the closing tag </boost_serialization> is not checked until the destructor is running.   
  
ANY other error will cause an exception before the destructor gets called. My try-catch block will catch it and I can do something about it. But a damaged </boost_serialization> will not be seen until the destructor runs, the exception is thrown inside the destructor and then the exception terminates the application.  
  
That is a very severe bug - it will appear rarely, but the consequences are very bad: a user loosing work.  
  
Or worse, consider this scenario (also a real life scenario): The application uses boost::serialization to save it's settings on close and restore the settings on the next start. One day the user closes the application and switches of the power too soon. The file get's written to disk, but the closing </boost_serializiation> tag is not written before the power goes off.  
  
The user will never be able to start the application again. The application will try to restore the settings on startup, but since the settings file is damaged, ~xml_iarchive will always throw inside its destructor, which will by default and by language design call terminate().  
  
If you really care about a well-formed XML file with a proper boost_serialization end tag, you need to verify the syntax either before or during reading. If you try to verify even part of the syntax only when the destructor runs it is too late.  
  
Or think of it this way: a destructor should clean up after an object. It should never verify something (except maybe really catastrophic errors, where terminating the application is preferred to continuing).  
  
Sure it is an error in the file, if the final tag is missing. But why terminate the application? With no way for a programmer to intercept that. All other errors in the file can be caught with a try-catch block. Only this particular problem will terminate the application. ==> bad.  
  
The problem is that you are throwing an exception inside a destructor. That will call "terminate", because the destructor is "noexcept". No library should ever do that IMHO (or at least provide clear instructions on what the application can do).  
  
A library that "terminate()"s my application without warning, without a dump file, without any chance of a crash handler, is buggy.  
  
Especially when reading a file. This could even have security implications. Think of an important process which an attacker can crash by overwriting a byte or sending a malformed request via TCP/IP.

---

## Comment 3

> Username: correaa  
> Created at: 2020-09-10 21:32:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-690743501  

Yes, you raised good points. You convinced me that the problem is that the windup function can throw and it can do it from a `noexcept` destructor.   
My arguments cover the rest of the library parsing but not the windup call in the destructor.   
  
I have to experiment a little to understand more.  
  
BTW, this can happen in the writing stage as well, because the windup in the writing could run of space.   
Perhaps it is true that winup shouldn't check or throw anything because if that point was reached that means that the object is serialized or deserialized correctly already. The XML end tag is just a nice thing to have but nothing else (from the point of view of the library, not the XML standard).  
  
Regarding the technical point of throwing from destructors: you *can* specify `noexcept(false)`.   
This creates other problems as you say, but perhaps not for archives in practice because they have a non-standard semantics.  https://akrzemi1.wordpress.com/2011/09/21/destructors-that-throw/  
Perhaps this is part of the solution because in your use a try block can be put in a way that guaranties that a single exception is on the fly even if thrown form the destructor?

---

## Comment 4

> Username: correaa  
> Created at: 2020-09-10 23:34:18 UTC  
> Updated at: 2020-09-11 15:52:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-690786015  

Ok, I tried a few little examples of a truncated file (missing `>`) and I agree with you that there is no way to _modify_ the terminating behavior without changing the library, which it self should be considered a bug.  
And for the reasons you layout this is different than any other problem in other parts of the xml file.  
  
(Unfortunately the xml archive library is provided as a compiled library so my experiments ended there for lack time to set up a compilation enviroment. So I played out with the headers only (adding `noexcept(false)` in all destructors but I think it doesn't help without recompiling the whole library).  
  
But, If you check the implementation https://github.com/boostorg/serialization/blob/6af3783a2c021a913b64b1e19c542b6977758931/include/boost/archive/impl/xml_iarchive_impl.ipp#L187-L200  
  
```c++  
template<class Archive>  
BOOST_ARCHIVE_DECL  
xml_iarchive_impl<Archive>::~xml_iarchive_impl(){  
    if(boost::core::uncaught_exceptions() > 0)  
        return;  
    if(0 == (this->get_flags() & no_header)){  
        gimpl->windup(is);  
    }  
}  
```  
  
The interaction with `uncaugth_exceptions()` make me think that the the destructor _was_ designed to throw exceptions if it can (if no other exception is on the fly), so following the logic I think the real missing piece is that the original intention was that the destructor (and some bases) would be `noexcept(false)` which exactly the example in https://www.boost.org/doc/libs/1_74_0/libs/core/doc/html/core/uncaught_exceptions.html, like this below. But only @robertramey can confirm.  
  
```c++  
template<class Archive>  
BOOST_ARCHIVE_DECL  
xml_iarchive_impl<Archive>::~xml_iarchive_impl() noexcept(false){ // CAN THROW EXCEPTIONS??  
    if(boost::core::uncaught_exceptions() > 0)  
        return;  
    if(0 == (this->get_flags() & no_header)){  
        gimpl->windup(is);  
    }  
}  
```  
  
**In conclusion, I agree that there is a bug in the library and the first thing to do is to enable exceptions in the destructor.  
Once this is fixed you will be able to catch the exception thrown from the destructor.  
To catch the specific error will require a try block narrow enough to avoid other flying exceptions.**   
  
---  
  
Now, a very hideous code and workaround to finish the afternoon.   
It turns out that because of the way that `windup` is implemented you can do this and call windup manually.   
Where the second call to windup (in the dtor) is not going to throw (I think because the header reader becomes disengaged).   
If you know the scope of the xml archive (or put in a unique_ptr with a custom dtor!).  
  
This works with archives with truncated `>` at the end for example.  
  
```c++  
#define BOOST_NO_MEMBER_TEMPLATE_FRIENDS // make gimp public  
... your includes  
#include<boost/archive/impl/basic_xml_grammar.hpp>  
template class boost::archive::basic_xml_grammar<char>; // crazy magic needed template instantiation  
  
...  
		std::ifstream ifs{name}; assert(ifs);  
                {  
		boost::archive::xml_iarchive xia{ifs};  
		xia >> a;  
		try{xia.gimpl->windup(ifs); /*sematic dtor*/}catch(...){puts("¯\_(ツ)_/¯ ");}  
                }  
```  
  
Note that although ugly it shows how to handle the error if a throwing destructor is enabled in the library. You have to catch the destructor and that implies a try block that exactly matches the scope of the archive.  
  
```c++  
		std::ifstream ifs{name}; assert(ifs);  
                try{  
		   boost::archive::xml_iarchive xia{ifs};  
		   xia >> a;  
		   //  it was xia.gimpl->windup(ifs);  
                }catch( specific_windup_error ){puts("¯\_(ツ)_/¯ ");}  
                catch( other errors ){ ... }  
```

---

## Comment 5

> Username: jmairboeck  
> Created at: 2020-09-11 15:03:20 UTC  
> Updated at: 2020-09-11 15:03:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691148734  

@correaa: I think I was incorrectly mentioned in your last comment. Did you mean someone else?

---

## Comment 6

> Username: hajokirchhoff  
> Created at: 2020-09-11 15:50:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691174397  

First, thanks for taking the time to read through my long description 👍   
  
> The interaction with uncaugth_exceptions() make me think that the the destructor was designed to throw exceptions if it can (if no other exception is on the fly), so following the logic I think the real missing piece is that the original intention was that the destructor (and some bases) would be noexcept(false)  
  
Actually no. I disagree here. I think the interaction with uncaught_exception simply means this:  
  
If an archive_exception is currently been thrown, the ~xml_iarchive destructor _must not call windup_. If it did, it would throw _another_ exception, thus terminating the program due to a double exception.  
  
Consider the following scenario:  
  
```  
try {  
   xml_iarchive my_archive(my_stream);  
   my_archive >> some_object;  
}  
catch (boost::serialization::archive_exception &e) {  
   stderr << "Sorry, the archive is damaged. Perhaps you forgot to increase serialize version number?" << endl;  
}  
```  
  
my_archive tries to stream into some_object. The stream may be syntactically correct, but a programming error causes an exception. Perhaps the programmer has modified some_object::serialize, but forgot to increase the version number. The archive can no longer read old versions and will throw an archive_exception.  
  
So far, so good and as designed. The programmer forgot to increase the version number, but the exception is thrown _during_ the read operation and can be caught with the catch statement.  
  
_BUT_ that means that there is an exception pending, when the ~xml_iarchive destructor is running. And that is, why, if uncaught_excptions() > 0, unwind is not called.  
  
Why? Because if the programmer has forgotten to increase the serialization version number, the read operation will find an error in the middle of the stream and throw an exception. It would be wrong to check for a   </boost_serialization>  tag, because the read operation did not read the entire stream. If unwind would be called, even though the read already threw an exception, unwind would check for </boost_serialization> and throw another exception ==> double exception, program terminates.  
  
_That_ imho is what the `uncaught_exception()` check is doing here. Check for the end tag only if no exception is currently being thrown (i.e. only, if no error occurred).  
  
I'll suggest a solution in my next comment.

---

## Comment 7

> Username: correaa  
> Created at: 2020-09-11 15:52:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691175519  

> @correaa: I think I was incorrectly mentioned in your last comment. Did you mean someone else?  
  
thanks, I meant @robertramey

---

## Comment 8

> Username: correaa  
> Created at: 2020-09-11 16:05:09 UTC  
> Updated at: 2020-09-11 16:06:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691182134  

> First, thanks for taking the time to read through my long description +1  
  
No problem, thanks for reading through my rambling thought process.  
  
> > The interaction with uncaugth_exceptions() make me think that the the destructor was designed to throw exceptions if it can (if no other exception is on the fly), so following the logic I think the real missing piece is that the original intention was that the destructor (and some bases) would be noexcept(false)  
>   
> Actually no. I disagree here. I think the interaction with uncaught_exception simply means this:  
>   
> If an archive_exception is currently been thrown, the ~xml_iarchive destructor _must not call windup_. If it did, it would throw _another_ exception, thus terminating the program due to a double exception.  
  
well that might be the intention, but the effect is that any other exception will also trigger this behavior, for example bad_alloc in the middle of the serialization will produce the same effect.  
So, yes, in particular it will not do the windup if there is an archive_exception on the fly but also with any other kind of exception, even some unrelated to archives or deserialization at all (e.g. if deserialziation is mixed with other code, which should be avoided anyway)>  
  
>   
> _BUT_ that means that there is an exception pending, when the ~xml_iarchive destructor is running. And that is, why, if uncaught_excptions() > 0, unwind is not called.  
>  
>   
> _That_ imho is what the `uncaught_exception()` check is doing here. Check for the end tag only if no exception is currently being thrown (i.e. only, if no error occurred).  
>   
  
Look at the example in https://www.boost.org/doc/libs/develop/libs/core/doc/html/core/uncaught_exceptions.html  
It is exactly for a case in which the destructor is `noexcept(false)`. There is no point in guarding for other exceptions if the library was not preparing for the possibility of windup to eventually throw.  
  
> I'll suggest a solution in my next comment.  
  
I think many solutions can be proposed, the minimal one is no ensure that the destructor is `noexcept(false)`.   
I actually would go a step further and say that this is a prototypical example of class that should be able throw from the destructor.

---

## Comment 9

> Username: hajokirchhoff  
> Created at: 2020-09-11 16:06:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691182999  

Regarding `noexcept(false)`  
  
Quoted from https://en.cppreference.com/w/cpp/language/destructor (scroll down until 'Exceptions')  
> Although std::uncaught_exception may sometimes be used to detect stack unwinding in progress, it is generally considered bad practice to allow any destructor to terminate by throwing an exception.   
  
Yes, your suggestion to make the destructor `noexcept(false)` to allow it throwing an exception could provide a solution. But I feel bad about it. Like the C++ reference said it is considered bad practice.  
  
I think the choice here is   
- either silently ignore a damaged closing XML tag  
- or make the destructor `noexcept(false)`, which I think is valid only since C++11 and is considered bad practice.  
  
I would argue for the first choice.   
  
In the trivial case, where I use a temporary xml_iarchive object to deserialize a stream a throwing ~xml_iarchive would probably work, but say I wanted to derive my own archive from xml_iarchive. The library allows, even encourages this. Or xml_iarchive is a member variable of my class. Throwing destructors are very rare and a programmer would not suspect that.   
  
Suddenly my class destructor would have to deal with an exception thrown by a destructor of a member variable. That's very unexpected and would definitely cause trouble.  
  
What's the damage OTOH of simply ignoring a bad XML end tag? The serialization XML is not really a full XML printer/parser anyway and XML here is only a 'container' of sorts for the actual serialization data. And this data has already been read and verified in full at this point.  
  
So who cares if the end tag is damaged. In this (boost::serialization) specific situation it's useless syntactic sugar. Let's silently ignore the damage. The archive has been read correctly. Why be overzealous and add esoteric C++ semantics just to alert the programmer that the end tag is damaged, even though the archive has been read correctly.  
  
BTW, this was the behaviour of the serialization library up to boost 1.65 anyway. I think the recent change to verify the end tag sounds good on paper, but the destructor is simply the wrong place to do it.   
  
The better way would be to change the design and verify the XML syntax before the destructor runs and use the destructor only to clean up - as they where intended originally.  
  
To sum things up:  
- it's considered bad practice  
- it's very unexpected  
- it _will_ cause enormous problems, if xml_iarchive is used as a base class or member variable of another class.

---

## Comment 10

> Username: hajokirchhoff  
> Created at: 2020-09-11 16:08:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691183821  

> >     If an archive_exception is currently been thrown, the ~xml_iarchive destructor must not call windup. If it did, it would throw another exception, thus terminating the program due to a double exception.  
  
> well that might be the intention, but the effect is that any other exception will also trigger this behavior, for example bad_alloc in the middle of the serialization will produce the same effect.  
  
No, it will not.  
  
It bad_alloc is thrown in the middle of the serialization, unwind will not be called. there will not be a double exception.  
  
That is what `if (uncaught_exceptions()>0) return;` ensures.

---

## Comment 11

> Username: hajokirchhoff  
> Created at: 2020-09-11 16:15:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691187105  

> Look at the example in https://www.boost.org/doc/libs/develop/libs/core/doc/html/core/uncaught_exceptions.html  
It is exactly for a case in which the destructor is noexcept(false). There is no point in guarding for other exceptions if the library was not preparing for the possibility of windup to eventually throw.  
  
I disagree. uncaught_exceptions can be used for other (mostly arcane) purposes. It has nothing to do with potentially throwing an exception in the destructor. Or shouldn't (Nice discussion here) http://www.gotw.ca/gotw/047.htm)  
  
I used it in one of my own libraries for diagnostic output. If uncaught_exception>0 I printed a message inside the destructor.  
  
But I would never use it like here.  
  
Like I said, making the destructor `noexcept(false)` might work at first, but _will_ cause problems when the archive is part of another class. I'd much rather silently ignore a malformed end tag - which is present only for internal reasons anyway and serves no useful purpose.  
  
Thanks for listening :)

---

## Comment 12

> Username: correaa  
> Created at: 2020-09-11 16:41:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691199473  

I agree with your solution message. Both solutions are equally valid and will be an improvement over the current situation. I think it ultimately depends on the goals of the XML archive and if formatting errors should _uniformly_ be detected by `catch`  (including end tag) .  
  
> > well that might be the intention, but the effect is that any other exception will also trigger this behavior, for example bad_alloc in the middle of the serialization will produce the same effect.  
>   
> No, it will not.  
>   
> It bad_alloc is thrown in the middle of the serialization, unwind will not be called. there will not be a double exception.  
>   
> That is what `if (uncaught_exceptions()>0) return;` ensures.  
  
By "produce the same effect" I mean that the windup will still potentially be not called, regardless of the kind of exception (there is nothing special about archive_exception).  
We are saying the same thing here. Just too many double negatives.  
  
> Like I said, making the destructor noexcept(false) might work at first, but will cause problems when the archive is part of another class. I'd much rather silently ignore a malformed end tag - which is present only for internal reasons anyway and serves no useful purpose.  
  
This is the key point, archive classes are not "values", making an archive part of another class makes that class have a special semantics by default. It is like making `stream` part of a class, you are in an Alice-in-wonderland kind of place, if you ever tried. Archives are a kind of relationship between objects in your program and the external world (in general).  
Archives have this *nature* regarless of how you solve the particular problem in C++ (e.g. ignoring errors or throwing from destructor). By making the destructor `noexcept(false)` you really mark this nature in the class instead of hiding it away.

---

## Comment 13

> Username: robertramey  
> Created at: 2020-09-11 17:14:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691214860  

I really haven't delved into this thread yet - it's a lot of work to go through.  I promise I will eventually.  This is an old question so I remember it more or less well.  Here are a few random observations:  
  
I've been around these arguments before and never really felt confident that I had "the answer".  Ultimately its a result of the destructor "closing" the xml_archive.  Or in more general terms, the destructor not being a "const" function.  We could break the "const" aspect by just not reading past the end.  But I wanted to preserver the possibility of concatenating multiple serializations in the same archive.  I don't know if that functionality is ever used.   
  
I'm generally against the idea of trying to "automagically" fix something that is not right.  In this case it's a missing end tag on the archive.  I know it seems attractive.  But in my (bitter) experience, it tends to push legitimate bugs downstream where they eventually turn up in some other guise - where they are much harder to fix and correct.  So I'm just inclined to prefer "terminate()" or something like that which forces the problem to be addressed as soon as possible.  It also means that assumptions about archive state are always preserved and subsequent calls will work or not work according the function specification and not depend on some invisible "side-effects".  
  
Ideally, I'd like to invoke this before the destructor is even called.  But that's not all that easy either.  
  
And of course the recursive exception calling is also a problem to try to correctly reason about.  
  
So, again I prefer just cutting things off as soon as possible with terminate().  or something like that.  As I write this, I'm not actually looking at the code and it's possible that the current code is at a variance with my current thinking.  As I'm sure all of you reading this thread know, sticking one's fingers in something like this easily ends up to be a lot  more complicated that it starts out looking.  
  
One saving grace is that this only occurs in the case of xml_archive due to the special "end" tags that xml demands.  On other archives it doesn't appear because no write is necessary in the destructor.  So that's good.  This means what we can use serialization in less forgiving environments like embedded systems - an important consideration for me.  
  
I also much appreciate the interest and efforts shown by all of you.  I don't really have any information oh the "health" of the serialization library and it's breadth of usage - after more than 16 years !!!.  So its nice to know that there is enough interest to keep improving / maintaining it.

---

## Comment 14

> Username: correaa  
> Created at: 2020-09-11 23:06:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-691347867  

This eventually reminded me of this talk  https://youtu.be/WjTrfoiB0MQ?t=1955 "CppCon 2015: Andrei Alexandrescu “Declarative Control Flow" Slide 25.  
  
It seems that ultimately a class that is responsible for transactional operations falls into this category sooner or later.  
  
@robertramey your work is much appreciated.

---

## Comment 15

> Username: code-affinity  
> Created at: 2021-11-26 14:30:28 UTC  
> Updated at: 2021-11-26 14:50:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-980016467  

One possible cause of truncation of the XML archive is #188.   Not knowing of this limitation, I have written plenty of code that writes to XML archives in scope guards (specifically using gsl::finally).  When the code in those scope guards is executed because of an exception, we run afoul of #188, which silently corrupts our XML archive.  The next time our application tries to read the XML archive, it terminates.    
  
We have been fighting this seemingly random application-terminating bug for years, and I have only just put these two bugs together to understand the cause.  In my opinion, this interaction is very serious.

---

## Comment 16

> Username: correaa  
> Created at: 2021-11-26 18:29:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-980267395  

> One possible cause of truncation of the XML archive is #188.   Not knowing of this limitation, I have written plenty of code that writes to XML archives in scope guards (specifically using gsl::finally).  When the code in those scope guards is executed because of an exception, we run afoul of #188, which silently corrupts our XML archive.  The next time our application tries to read the XML archive, it terminates.    
>   
  
What do you mean by "silently corrupt"? The end tag is not written or something worst?  
  
>   
> We have been fighting this seemingly random application-terminating bug for years, and I have only just put these two bugs together to understand the cause.  In my opinion, this interaction is very serious.  
  
  
What do you mean by random? What is the ultimate cause in your case? Running out of space?  
  
I think your case can be very illustrative of how what should be the right behavoir of XML OArchive. The reason is that in your case it is clear that there is an exception already on the fly even before the archive exists.   
  
Can you post a minimal working example of your scope guardes code?

---

## Comment 17

> Username: code-affinity  
> Created at: 2021-11-26 18:59:29 UTC  
> Updated at: 2021-11-26 19:29:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-980303491  

>What do you mean by "silently corrupt"? The end tag is not written or something worst?  
  
By corrupt, I only mean that the end tag is not written.  But the fact that an error occurred that prevented the XML archive from being completely written is not communicated to the calling code, which is what I mean by "silently".  
  
>What do you mean by random?  
  
It was not, of course, truly random.  But until I understood that the interaction of these two bugs was causing the crashes, it certainly seemed that way!  We've been trying to figure this out for years!  
  
I'll exemplify the problem with a sketch of some code:  
  
```  
void WriteToArchive(bool flag)  
{  
  // Writes to an XML archive, storing only the provided bool flag  
}  
  
void ReadFromArchive()  
{  
  // Reads from that same XML archive  
}  
  
void Level3()  
{  
  // Does something that rarely throws  
}  
  
void Level2()  
{  
  WriteToArchive(true)  
  auto _ = gsl::finally([this]() {WriteToArchive(false);});  
  
  Level3();  
  
  // Here is the 188 problem.  If Level3 throws an exception, the callable passed to  
  // finally() executes while an exception is in flight.  On exit from this function,   
  // the XML archive no longer contains well-formed XML  
}  
  
void Level1()  
{  
  try  
  {  
    Level2();  
  }  
  catch (...)  
  {  
    // Whatever  
  }  
  
  // Meanwhile, in code that is potentially very far removed from the Level2 call site:  
  // Here is the 220 problem.  If the XML archive no longer contains well-formed XML,   
  // the following call terminates the application.  
  ReadFromArchive();  
}  
```  
  
In our case, the conditions that caused the Level3 function to throw were quite rare, so it took us a long time to figure out that this sequence of events was responsible for the application terminations that were being reported from the field.

---

## Comment 18

> Username: correaa  
> Created at: 2021-11-27 09:49:01 UTC  
> Updated at: 2021-11-27 09:53:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-980532914  

@code-affinity ok, I think the context is clear and, yes, the current implementation of XML OArchive is preventing you to properly close the XML tag because of this line https://github.com/boostorg/serialization/blob/337b3fbc7c4648d6f95f863546b9482500c8dec5/include/boost/archive/impl/xml_oarchive_impl.ipp#L132-L133  
  
I don't know the justification of this line; I think it was a (naive?) attempt to detect if an exception was thrown *while* and *by* population the XML archive but as you show that is not the only possible cause.  
  
I think your use case is a clear example of why this line is not good to have.   
I think several changes are needed to the destructor of XML Archive, starting by removing this line.  
  
I expressed my ideas before, I think this one of these cases where making the destructor noexcept at all cost and no class taking responsibility of failure ends up creating the worst possible situation (a silent irrecoverable state).  
  
One workaround for your case (not a general solution) is that the archive maintains a count of `uncaught_exceptions` from construction and check if the number of `uncaught_exceptions` stayed the same.   
That will make this number part of the state of the archive at the implementation level.  
This sounds kind of ridiculous, and I would agree, this is because it is a patch upon a patch to a more general problem.  
  
Each time I look at this problem I convince myself more that the actual solution is to make the archive destructor no noexcept instead of heroically try to hide failures, which end up biting you one way or another when trying to load a truncated XML 15 weeks later.  
Many people believe that destructors cannot be no noexcept ever or that you should not do it even if you can, this is not true.   
The reality is that a class that can throw from the constructor has a special (I would call it "transactional") semantics and this attribute just makes it clear.  
  
I think @robertramey is cautious of breaking old code by changing this behavior, my opinion is that old code is already broken with respect to failures on the closing tag anyway.  
  
Maybe your code example convinces Robert to remove this particular line to start with.

---

## Comment 19

> Username: robertramey  
> Created at: 2021-11-29 17:22:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-981848555  

I am interested in getting this definitively fixed.  I believe I've tried before but broke something else as a side effect.  So I'm pretty gun shy.  I'm reluctant to mess with it without delving deeply into it which I can't do now as I'm bogged down in other stuff.  
  
BUT - looks like you have deleted deeply into it.  So how about reposting the issue as a PR.  You can all check it and make sure that you all agree it's the right fix then I'd merge it in.  Here are a couple of considerations that should be kept in mind.  
  
New code should  
1. create new archives which don't have the problem.  
2. be able to add new as well as old archives.  
3. likely include some simple tests to prove that it's fixed.  
4. should increment the "archive version" number in the header so archives newly created can be recognized and read accordingly.  
5. should include a better analysis and method of handling exceptions, placing the xml end tag on the newly created archive.  
6. will likely need a new archive opening "option" for end tag.  
7. perhaps make the ending tag optional  
7. I'm not sure what else.  
  
So this is an opportunity to become an official Boost Library Author.  Many (all recent) corrections/enhancements have been added to the serialization library in this manner.  Since you can do it on company time - since your company needs this - you're effectively getting paid to work as a boost developer - something that not even I get!   Please say yes (more than one is OK).  This would greatly help, me, boost, the serialization library and in some small way your career.  
  
Robert Ramey

---

## Comment 20

> Username: correaa  
> Created at: 2021-11-30 02:08:10 UTC  
> Updated at: 2021-11-30 02:15:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-982212292  

Hi @robertramey , I would be very happy to help.   
  
Do you mind writing (in the Wiki?) a small tutorial on how to contribute the project, compile and test the library locally; starting from the line `git clone git@github.com:boostorg/serialization.git`?  
  
I tried several times but I don't know where to start with this library and other (non-header) Boost libraries.  
  
This is the farthest I went to far (in Linux):  
  
```  
 $ git clone git@github.com:boostorg/serialization.git  
 $ mkdir serialization_build   
 $ cd serialization_build  
 $ cmake ../serialization   
-- The CXX compiler identification is GNU 10.3.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Configuring done  
CMake Error at CMakeLists.txt:47 (add_library):  
  Target "boost_wserialization" links to target "Boost::array" but the target  
  was not found.  Perhaps a find_package() call is missing for an IMPORTED  
  target, or an ALIAS target is missing?  
...  
```

---

## Comment 21

> Username: correaa  
> Created at: 2021-11-30 03:01:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-982237051  

Ok, I got it (partially), each library is supposed to be compiled as a submodule.  
  
I was able to download the library, and compile, but not run tests yet.  
  
```  
git clone --recurse-submodules git@github.com:boostorg/boost.git  
cd boost  
mkdir build  
cd build  
cmake ..  
```  
  
(works)  
  
I have to figure out how to run the tests now.

---

## Comment 22

> Username: danyhm  
> Created at: 2022-11-15 18:14:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/220#issuecomment-1315694609  

I have the exact same problem with XML serialization end tag  
the end tag is not being written, I had to change it like this   
```  
{  
  boost::archive::xml_oarchive oa(ofs);  
  oa << BOOST_SERIALIZATION_NVP(obj);  
} // oa destructor invoked - puts end tag on archive  
```  
however, I'm still getting the abort error!!
