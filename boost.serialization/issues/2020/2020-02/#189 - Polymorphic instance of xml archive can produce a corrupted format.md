# #189 - Polymorphic instance of xml archive can produce a corrupted format [Open]

> Username: correaa  
> Created at: 2020-02-01 05:08:07 UTC  
> Updated at: 2020-02-03 21:50:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/189  

This code generates an xml file that cannot be read by deserialization:  
  
```c++  
#include<fstream>  
#include <boost/serialization/nvp.hpp>  
#include<boost/archive/polymorphic_xml_oarchive.hpp>  
  
int main(){  
	int i = 5;  
	{  
		std::ofstream ofs{"file.xml"};  
		boost::archive::polymorphic_oarchive* poa = new boost::archive::polymorphic_xml_oarchive(ofs);  
		(*poa) << i;  
		delete poa;  
	}  
}  
```  
  
file.xml  
```  
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="16">  
5</boost_serialization>  
```  
  
Of course the problem is that I "forgot" to add the named value pair. For non-polymorphic archives this would produce a compilation error, but fails silently in the polymorphic case. I think a more correct behavior would be either 1) to make a runtime-time check that the passed object is a named-value pair when passing to an actual XML archive, or 2) force polymorphic archives to only accept named-value pairs.  
  
In this case is obvious but in the case were many runtime cases are considered (including other archives format that do not require named-value-pair), this is difficult to spot. The error only happens when trying to load the file.  
  
For reference, the workaround is to do this:  
```c++  
		(*poa) << boost::serialization::make_nvp("i", i);  
```  
with the output:  
```  
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="16">  
<i>5</i>  
</boost_serialization>  
```  
  
---  
  
Any how I think this points out to another possibility that could make the XML archive more powerful, and that is that if no named-value pair is passed to it, the archive should generate unique names, (possibly based partially on the type information + some uuid or fixed string, e.g. "unnamed").

---

## Comment 1

> Username: robertramey  
> Created at: 2020-02-01 16:39:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581046737  

>For non-polymorphic archives this would produce a compilation error, but fails silently in the polymorphic case  
  
Looks like a bug to me.  both kinds of archives should function in the same way.  I'll look into it.  
  
>Any how I think this points out to another possibility that could make the XML archive more powerful, and that is that if no named-value pair is passed to it, the archive should generate unique names, (possibly based partially on the type information + some uuid or fixed string, e.g. "unnamed").  
  
Waaaaaay too clever.  My philosophy is: "here are the rules.  If you break them you'll get an error message".  That way no one has to start guessing what the rules are.

---

## Comment 2

> Username: robertramey  
> Created at: 2020-02-01 16:45:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581047234  

Minor note:  I would have written the test as  
  
`#include<fstream>  
#include <boost/serialization/nvp.hpp>  
#include<boost/archive/polymorphic_xml_oarchive.hpp>  
  
int main(){  
	int i = 5;  
	{  
		std::ofstream ofs{"file.xml"};  
		boost::archive::polymorphic_oarchive & oa = boost::archive::polymorphic_xml_oarchive(ofs);  
		oa << i;  
	}  
}`

---

## Comment 3

> Username: correaa  
> Created at: 2020-02-01 21:46:47 UTC  
> Updated at: 2020-02-01 21:49:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581072564  

>  `boost::archive::polymorphic_oarchive & oa = boost::archive::polymorphic_xml_oarchive(ofs);`  
  
mmm, that generates a reference to a temporary, not sure if it is supposed to work. Maybe a `const&` version could be made to work. (It is a separate issue anyway.)  
  
The reason I found the bug is that I am really using the `polymorphic_archives` for runtime decision, which may not be the original intent of the feature, but exposed the bug in a clear way.  
  
```c++  
enum format {xml, txt, bin};  
namespace barch = boost::archive;  
namespace bs11n = boost::serialization;  
template<class T>  
void save(T const& a, std::string name, format f){  
	std::ofstream ofs(name);  
	*[&]()->std::unique_ptr<barch::polymorphic_oarchive>{switch(f){  
		case xml: return std::make_unique<barch::polymorphic_xml_oarchive   >(ofs);  
		case txt: return std::make_unique<barch::polymorphic_text_oarchive  >(ofs);  
		case bin: return std::make_unique<barch::polymorphic_binary_oarchive>(ofs);  
	};}() << a; // needs to be "bs11n::make_nvp("root", a);" to avoid bug   
	assert(ofs);  
}  
```

---

## Comment 4

> Username: robertramey  
> Created at: 2020-02-01 23:02:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581078527  

> The reason I found the bug is that I am really using the polymorphic_archives for runtime decision, which may not be the original intent of the feature, but exposed the bug in a clear way.  
  
Actually it's the exact intent of the feature.  It permits one to write programs which can invoke serialization for any archive type and have the determination of that type set at execution time.  
  
It turns out that xml_archives have a requirement (name of item) which is not part of the archive concept.  At compile time one doesn't know what kind of archive is going to be used so it can't be checked at compile time.  It might be possible to permit a user to specify xml requirements at compile time but then it might not make sense to use a polymorphic archive.   
  
There are a number of ways one could go here, including just doing nothing, so I would have to think about this.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-02-01 23:16:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581079654  

by the way - your code above conflicts with the concept of polymorphic archive which is designed to not "know" what kind of archive is being used and to access everything through the base class.  A more likely way of doing it would be:  
  
namespace barch = boost::archive;  
namespace bs11n = boost::serialization;  
  
template<typename Archive, typename V>  
save_value(const V &v){  
static_assert(is_archive<Archive>(), "is serialization archive");  
polymorphic_archive & pa = Archive(name);  
pa << data items ...;  
// Archive destroyed on leaving context  
}  
  
Then you could compile without having all the archive types compiled until needed - lots of variations on this.

---

## Comment 6

> Username: correaa  
> Created at: 2020-02-03 03:53:26 UTC  
> Updated at: 2020-02-03 04:15:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581225804  

> It turns out that xml_archives have a requirement (name of item) which is not part of the archive concept. At compile time one doesn't know what kind of archive is going to be used so it can't be checked at compile time. It might be possible to permit a user to specify xml requirements at compile time but then it might not make sense to use a polymorphic archive.  
  
Sure, this is the typical problem with runtime polymorphism, the (violation) of concept constrain have be reflected at runtime.  
  
> There are a number of ways one could go here, including just doing nothing, so I would have to think about this.  
  
I think two of the possible ways is what I called options (1) and (2) in my first emails. There are actually three or more options: (a) is produce an error (e.g. throw) (b) make the concept of polymorphic archive more constrained (needs to ALWAYS give named-value pair, even if name is going to be unused) (c) produce automatic or default names on-the-fly when archive requires a name.  
  
(Honestly, (b) has also a compile-time analog, the point is that when adding generic serialization code I have to assume that nvp will be required always. That is why I asked in other issues for a nvp-concept or relaxing the name-requirement for the xml archive.)  
  
Also, it is hard to understand your last email because the markdown chopped important parts of the code.    
  
I think we are talking about different uses (at different levels), what I toyed with was to save in different formats depending on the extension of the file (runtime given). This is a more concrete example:  
  
```c++  
template<class T>  
void save_to_file(T const& a, std::experimental::filesystem::path p){  
	std::ofstream ofs(p);  
	*[&]()->std::unique_ptr<barch::polymorphic_oarchive>{  
		     if(p.extension()==".xml") return std::make_unique<barch::polymorphic_xml_oarchive   >(ofs);  
		else if(p.extension()==".txt") return std::make_unique<barch::polymorphic_text_oarchive  >(ofs);  
		else if(p.extension()==".bin") return std::make_unique<barch::polymorphic_binary_oarchive>(ofs);  
		else assert(0);  
	}() << a; // or bs11n::make_nvp("root", a);  
	assert(ofs);  
}  
```  
  
Again, this might or might not be a good use, but exposes the current bug and it shows that it could happen under common uses.

---

## Comment 7

> Username: robertramey  
> Created at: 2020-02-03 04:33:29 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581232565  

I was toying with (b) coupled with a header flag which would force the archive to throw if it wasn't an nvp.   But I don't have any time available to work on this now.  I do think that characterizing this as a "bug" is a little harsh.  The bug is not using nvp when one might use an xml polymorphic archive.  I'm not sure failing to catch a bug is a bug.  On the other hand, the serialization library is pretty good at detecting user errors and showing how to fix them.  Maybe I'm a victim of my own success.  
  
I stand my my criticism that the nested if statements are not a good way to use polymorphism.  It's not extendable.  My approach eliminates the enum xml, text, binary, ... and replaces it with xml_oarchive type, text_oarchive type, etc...  It's inherently extensible to any archive type existing, future, new, old or custom.

---

## Comment 8

> Username: alfC  
> Created at: 2020-02-03 21:50:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/189#issuecomment-581635832  

On Sun, Feb 2, 2020 at 20:33 Robert Ramey <notifications@github.com> wrote:  
  
> I was toying with (b) coupled with a header flag which would force the  
> archive to throw if it wasn't an nvp.  
>  
I think if you need backward compatibility (if anyone used polymorphic  
archives) this is the only realistic option you are left with.  
  
But I don't have any time available to work on this now.  
>  
Yes, sure. I was mostly wondering if this was a simple oversight or  
something you didn’t think about. I wish I had the time to learn to compile  
your library and send a proper tested PR.  
  
> I do think that characterizing this as a "bug" is a little harsh. The bug  
> is not using nvp when one might use an xml polymorphic archive.  
>  
Sure, the key word here is “might”. This is typical problem of hierarchies,  
the xml derived class imposed requirements over the base, and that adds too  
many responsibilities on the base.  
  
The proper way is to have two kinds of polymorphic bases, including one for  
archives that require nvp. But can produce overengineering.  
  
> I'm not sure failing to catch a bug is a bug.  
>  
Sure, you are free to call it undefined behavior at any point but it is not  
the typical design for runtime polymorphism.  
  
> On the other hand, the serialization library is pretty good at detecting  
> user errors and showing how to fix them. Maybe I'm a victim of my own  
> success.  
>  
To be honest, the library detected the error when loading the file later.  
So, yes, expectations are high.  
  
Thank you again for your powerful library.  
  
> I stand my my criticism that the nested if statements are not a good way  
> to use polymorphism. It's not extendable. My approach eliminates the enum  
> xml, text, binary, ... and replaces it with xml_oarchive type,  
> text_oarchive type, etc... It's inherently extensible to any archive type  
> existing, future, new, old or custom.  
>  
  
Note that there is no enum in the second (last) code example. If you need  
to determine the format from the file name there are not many options left.  
But don’t worry about this; I understand your point and it was simply a way  
to show to produce the error in a certain context.  
  
In any case the behavior described can appear with either approach.  
  
Thanks  
A
