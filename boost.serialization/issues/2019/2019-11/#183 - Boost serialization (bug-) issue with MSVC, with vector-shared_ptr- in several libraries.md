# #183 - Boost serialization (bug?) issue with MSVC, with vector<shared_ptr> in several libraries [Open]

> Username: gri38  
> Created at: 2019-11-26 15:32:57 UTC  
> Updated at: 2024-02-19 07:29:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/183  

We use boost serialization in ours projects, both on linux (gcc) and Windows. We encounter a problem only on Windows (for "every" versions of Windows (7 and 10) and of MSVC: Visual 2010 and 2015, ie. MSVC++ 10.0 _MSC_VER == 1600 or MSVC++ 14.0 _MSC_VER == 1900).  
  
**Here is the problem summarized**: When I serialize a class  
  
- which have base class  
- and have 2 vector of shared_ptr of ClassB, with the same contents, and B inherits from the same base class  
  
the serialization works, but the deserialization doesn't ("input stream error").  
![image](https://user-images.githubusercontent.com/26554495/69647542-f9655680-1069-11ea-9b46-07cd7e96c5b6.png)  
  
It's very strange:  
  
- if I remove the base class, it works.  
- if I inline the serialization code (empty function) of the base class, it works.  
- if the 2 vectors don't have the same content, it works !!  
- if I register the ClassBase in the serialize method of ClassTest, it works. I.e.:  
  
```  
    // in ClassTest:  
    template <class Archive>  
    void serialize(Archive & ar, const unsigned int version) {  
        ar.template register_type<ClassBase>();  
        ...  
    }  
```  
  
**Some details**  
  
**When it works** on linux, and when it works on Windows with ClassBase type registration in the archive, we have the same xml output.  
It's worth noting that:  
  
- boost_serialization is version 17 on Windows and 9 on linux (I didn't tried to update boost on linux yet).  
- On BaseClass xml tag, the tracking_level="1", so we have object_id.  
  
**And when it doesn't work** (once again: the serialization of ClassTest is OK, but cannot deserialized "input stream error"):  
  
- The main difference is in the serialization of ClassTest: the ClassBase part has no tracking_level.  
- However it's not the only cause, because if the vectors are differents (same 2 first items, and a third item in one vector), tracking_level of ClassBase in ClassTest part is "0".  
  
I've posted a visual studio solution [here](https://1drv.ms/u/s!Aoy4ax0S3ZS5gkxv1UIvRUd32oVO?e=ACVi4c).  
  
Thanks a lot for your help. And if any clarification is needed, I'm available of course !  
  
N.B.: I compiled with the last development version of boost serialization, the issue remains.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-11-26 16:16:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-558703929  

First of all - love the diagram.  What did you use to create it?  
  
I'll take a look at this.  In the meantime you can try following.  
  
I recently fixed and obscure bug which might be related to this.  So please double check that you're using the lastest version of the library.  On the other hand, it's possible that what you're reporting is an effect of the bug fix.  That is, fixing the other bug created this one.  The infamous whack-a-mole game.  
  
Note that I don't have windows machine available so it's not going to be easy for me to isolate this. After 15 years of more and more obscure refinements, any bug is very difficult to isolate this.  But I'll take a sincere look at this.  I strive for perfection.

---

## Comment 2

> Username: gri38  
> Created at: 2019-11-29 15:05:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-559817443  

Hello.  
  
I use Enterprise Architect for UML.  
  
As you demanded, I double-checked I use the last version of the library, and in fact yes I was using, but at first glance I didn't notice the compilation failed.  
So I did my tests with the "official" serialization library.  
  
The sources I downloaded today (2019-11-29) have this problem:  
init_from_stream.cpp  
.\boost/serialization/nvp.hpp(19): fatal error C1083: Cannot open include file: 'boost/core/nvp.hpp': No such file or directory  
  
I'm surprised: am I missing something?

---

## Comment 3

> Username: glenfe  
> Created at: 2019-11-29 16:26:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-559838230  

You downloaded the latest sources of Serialization, but you're using it with an older version of other Boost libraries.   
  
This might be because you're using your downloaded Serialization with system installed older versions of other Boost libraries (like Core).  
  
You need to use the latest version of Serialization with the latest version of other Boost libraries.

---

## Comment 4

> Username: gri38  
> Created at: 2019-11-29 16:46:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-559842234  

Thanks.  
I use boost 1.71 which is the last version.  
Do I need to download boost development version? If so: where ?

---

## Comment 5

> Username: glenfe  
> Created at: 2019-11-29 17:33:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-559851881  

You can:  
* Download the Boost 1.72 beta at https://www.boost.org/users/history/version_1_72_0.html  
or  
* Clone Boost from Git:  
```  
git clone --recursive https://github.com/boostorg/boost  
```

---

## Comment 6

> Username: gri38  
> Created at: 2019-12-03 08:28:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-561055373  

The last sources of serialization with boost 1.72 fixes the problem I encounter on the attached example.  
We're working on our real software to check if it solves the problem we have.  
I'll post the result when I'll have it, but in the meanwhile I think you can consider this issue as invalid (or fixed).

---

## Comment 7

> Username: gri38  
> Created at: 2019-12-03 11:54:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-561134936  

OK... sorry for the turnarounds, but finally no: last sources of boost and serialization don't fix my issue.  
The solution [here](https://1drv.ms/u/s!Aoy4ax0S3ZS5gkxv1UIvRUd32oVO?e=ACVi4c) still ends up with "input stream error".

---

## Comment 8

> Username: gri38  
> Created at: 2019-12-04 14:18:07 UTC  
> Updated at: 2019-12-04 14:19:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-561665162  

If it can help, here are some things I tried:  
  
- The same project on linux with boost 1.72 (not with latest serialization) : it works.  
- The same project on windows, with boost_1_59: it still fails  
It seems to indicate: no regression in boost. It's an issue related to windows.  
  
I tried with simple win32 exe and dll: no MFC, nothing else (in order not to disturb).  
  
Once again: just by adding one shared pointer in one vector only makes the deserialization work! The xml files are strictly the same except one vector has one item more.  
However: if the additional shared pointer is added in the second vector (the one serialized with object_id_reference), it fails (sometimes):  
  
Above I write the codes that make the deserialization working or failing.  
When both vectors have the same content, it fails.  
When I add another shared_ptr (b3), it depends:  
  
```  
object_Save.vectB_1.push_back(b1);  
object_Save.vectB_1.push_back(b2);  
object_Save.vectB_1.push_back(b3); // b3 added in vectB_1  
object_Save.vectB_2.push_back(b1);  
object_Save.vectB_2.push_back(b2);  
```  
The code above works: deserialization works.  
  
```  
object_Save.vectB_1.push_back(b1);  
object_Save.vectB_1.push_back(b2);  
object_Save.vectB_2.push_back(b3); // b3 added in vectB_2 at the beginning   
object_Save.vectB_2.push_back(b1);  
object_Save.vectB_2.push_back(b2);  
```  
The code above works: deserialization works.  
  
```  
object_Save.vectB_1.push_back(b1);  
object_Save.vectB_1.push_back(b2);  
object_Save.vectB_2.push_back(b1);  
object_Save.vectB_2.push_back(b2);  
object_Save.vectB_2.push_back(b3); // b3 added in vectB_2 at the beginning   
```  
The code above doesn't work: deserialization doesn't work.  
  
  
```  
object_Save.vectB_1.push_back(3); // b3 added in vectB_1, at the beginning   
object_Save.vectB_1.push_back(b1);  
object_Save.vectB_1.push_back(b2);  
object_Save.vectB_2.push_back(b1);  
object_Save.vectB_2.push_back(b2);  
```  
The code above doesn't work: deserialization doesn't work.

---

## Comment 9

> Username: gri38  
> Created at: 2019-12-04 14:50:14 UTC  
> Updated at: 2019-12-04 21:36:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-561679113  

More water in the mill, if it helps:  
If B.dll becomes B.lib and I link everything static, it works in all cases, and the output xml is strictly the same as in linux. Everything means: boost libraries (libboost_serialization-vc140-mt-s-1_59.lib) and also MFC libraries. And my B.lib is linked statically to the exe.  
  
However, it's not a acceptable solution for my project...

---

## Comment 10

> Username: robertramey  
> Created at: 2020-02-20 06:06:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-588626277  

OK - I've looked at all of the above as well as your test case.  First of all, I appreciate all the effort you've invested in your efforts to isolate this issue.  Summarizing your results it seems the failure occurs when all of the following are true:  
  
a) it's on windows  
b) class be is implemented as a DLL  
c) vectors are are repeated.  
  
Each of the above touches on tricky aspects of the code.  Windows DLL caused problems with visibility and creating of singletons in different address spaces  The repeated data items are followed so that they save space.  Normally I would take your example and trace through it on a debugger and get to the bottom of it.  But I don't use a windows machine any more.  I'm also super strapped for time and would have to take time off from paying work to spend time on it.  So I'm not sure what to tell you.  It could well be a very obscure bug.  
  
One thing that I might suggest is that you make sure that the archive is destroyed before the archive is closed.  In your case you close the stream.  Then when the archive goes out of scope it is destroyed. XML archives write an end tag - to the now closed stream.  The easiest way to address this is to enclose each TEST_Write and TEST_Read section in it's own level of braces.  When the archive and stream go out of scope, the will be destroyed in the opposite sequence that they were created in - which is the sequence we desire.  I don't know if this will help, making this simple change will eliminate one possible source of weird behavior.  
  
Sorry I can't be more helpful

---

## Comment 11

> Username: gri38  
> Created at: 2020-02-20 08:10:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-588732862  

Hello Robert.  
  
Thanks for your feed back.  
I ensured to close the stream after the archive is destroyed, but it didn't solve.  
  
Anyway, thanks for the visibility you gave me.

---

## Comment 12

> Username: gri38  
> Created at: 2021-04-02 08:31:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-812413785  

Hello, @robertramey .  
  
I'm back on the project with this issue ;-)  
Let me please ask you again about this bug: is there any chance that it is addressed?  
I ask only for visibility, not for pressure.  
  
Thanks.

---

## Comment 13

> Username: robertramey  
> Created at: 2021-04-02 12:54:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-812517226  

Cleary you have a difficult situation here.  And I think that it's particular to you.  This might be because of a bug in the app, or it might be due to bug in the library which is not triggered by anyone else but your app.  It's happened before - that's why any bugs found are getting harder to fix.  
  
There is a situation in xml archives which is currently unresolved.  But it occurs only when there is some sort of io error in the stream which the archive is using.  I don't know if this applies to you or not.  
  
At this point I can only suggest that I start working on your particular configuration.  This would have to be in the form of paid consultant work.  I realize that it's really annoying to have to consider paying me to resolve a situation that is likely to have been created by me.  On the other hand, if you consider all the benefit your company has gotten from the serialization library, Boost and open source in general, it might make this easier to swallow.  There are a number of ways this could work.  
  
a) we set up access to a machine at your place which I could use remotely.  This can work but depending on lots of things can also be cumbersome.  
b) I can setup a windows machine here with everything to reproduce the problem and work on it here.  
c) I can go to Grenoble for a week and we can work together on the issue.  I would estimate a week would be necessary.  This would have the advantage of personal contact and communication which I think would be helpful in a case like this.  It's also possible that in reviewing your code in light of Boost, we might find opportunities which neither of us might see alone.  
  
My preference would be for c).  I would also like to do this as for me it would be a nice trip.  I haven't been to France in 35 years.  This alternative might be more expensive, but I doubt it would all that much more than the others.  Due to issues I have here, I would likely not be available to do this until July.  
  
Sorry I can't give a better suggestion.  
  
There is one thing.  Does this occur on all archive classes or just xml?

---

## Comment 14

> Username: pdimov  
> Created at: 2021-04-02 16:08:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-812595006  

I tried the attached Visual Studio solution, but it requires MFC, which I don't have installed, so the projects don't open. It also uses NuGet packages for Serialization, which is also unnecessary; I want to be using the copy of Boost I have. @gri38 can you please make another solution that doesn't require these?

---

## Comment 15

> Username: gri38  
> Created at: 2021-05-07 13:57:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-834421567  

Hello. Sorry for delay.  
@robertramey unfortunately we cannot afford this kind of intervention. In the meantime, we have worked-around the bug.  
It seems the bug is indeed in the library, because the minimalist solution I sent is quite simple.  
  
@pdimov I attach a solution without MFC nor nuget. Let me know if it's ok for you.  
[Test_Boost_Serialization.zip](https://github.com/boostorg/serialization/files/6442025/Test_Boost_Serialization.zip)  
  
I remove boost libraries from the archive so that you can use your own copy of boost (and also to be able to attach the zip in this post). The expected tree is:  
  
![image](https://user-images.githubusercontent.com/26554495/117460305-ac6afc80-af4c-11eb-8a33-ac35d6d66223.png)  
  
  
Thanks for your work.

---

## Comment 16

> Username: pdimov  
> Created at: 2021-05-07 15:23:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-834518947  

The fix is to mark `ClassBase` as `track_always`:  
```  
BOOST_CLASS_TRACKING(ClassBase, boost::serialization::track_always)  
```  
The problem is that the default `track_selectively` sees that the first `ClassBase` that is serialized, the base of `ClassTest`, is not serialized via a pointer, and it is therefore marked as `tracking_level="0"`. This is then inherited by the `ClassBase` bases of the elements for some reason. I don't know if this is a bug or not; the code is too complicated for me to follow.

---

## Comment 17

> Username: gri38  
> Created at: 2021-05-07 16:25:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-834593462  

Wahoo I'm impressed by   
- your reactivity  
- even if you don't know if it's a bug you know the fix and its explanation  
  
I'll test in out bigger context and let you know.  
  
Thx !

---

## Comment 18

> Username: robertramey  
> Created at: 2021-05-07 16:44:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-834612594  

I'm impressed as well !!!  I love boost, it's developers and it's users!  
  
The serialization traits "system" does have a couple of "holes" which would be very, very, very difficult to definitively fix at this point.  Part of the difficult is that any such fix would have to be compatible with all existing archives.  I've learned through bitter experience that it is very tricky to mess with this.  
  
I believe this problem is related to one of the holes.  If a type is not serialized through a pointer, the tracking=level is set to zero.  The idea is that there's no point in tracking something which can never be repeated.  Then a new program is written which imports the existing archive and re-exports via pointer.  So things can get out of sync.  Or something like that.  Truth is I almost never get bug reports on the serialization library these days.  And those that I do get are often solved by users.  This is a great thing.

---

## Comment 19

> Username: pdimov  
> Created at: 2021-05-07 17:08:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-834628454  

The cause of the problem was that when trying to read the second pointer of the second vector, the `object_id` array had only two entries, whereas it needed to have four:  
```  
		<item class_id="3" tracking_level="0" version="1">  
			<px class_id="4" tracking_level="1" version="0" object_id="_0">  
				<ClassBase object_id="_1"></ClassBase>  
				<m_i>2</m_i>  
			</px>  
		</item>  
		<item>  
			<px class_id_reference="4" object_id="_2">  
				<ClassBase object_id="_3"></ClassBase>  
				<m_i>5</m_i>  
			</px>  
		</item>  
```  
Since the second pointer referenced `_2`:  
```  
		<item>  
			<px class_id_reference="4" object_id_reference="_0"></px>  
		</item>  
		<item>  
			<px class_id_reference="4" object_id_reference="_2"></px>  
		</item>  
```  
this errored. I suppose the system got somehow confused and issued object IDs for the `ClassBase` objects when writing, but did not track them when reading?  
  
When you add a third pointer to the first vector, reading works, by chance, because there are now three objects in the array when reading, and that's enough to resolve `_2`. But it resolves to the wrong object (the third one, instead of the second one.)

---

## Comment 20

> Username: pdimov  
> Created at: 2021-05-07 19:35:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-834723239  

When saving the vector elements, this check: https://github.com/boostorg/serialization/blob/337b3fbc7c4648d6f95f863546b9482500c8dec5/src/basic_oarchive.cpp#L263  
skips saving the tracking level of the inner `ClassBase` types, because it sees it already initialized by the outer `ClassTest` object.  
  
I'm not sure whether the library intends to support types that are serialized both by value and by pointer, without them being marked `track_always`.

---

## Comment 21

> Username: robertramey  
> Created at: 2021-06-01 19:23:44 UTC  
> Url: https://github.com/boostorg/serialization/issues/183#issuecomment-852386196  

Remembering back 16 years ago.  
  
track_selectively (the default) would track if it's serialized by pointer and skip tracking otherwise.  When the object is serialized, the tracking trait is written into the archive.  Sounds reasonable.  
  
But what happens when we serialize (maybe in another program) by value - then tracking is suppressed to save time/space.  
  
But now we have a conflict - or something like that.  Did Peter's solution address your problem?
