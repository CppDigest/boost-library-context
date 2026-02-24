# #132 - Serialization of Boost MultiArray [Open]

> Username: correaa  
> Created at: 2018-10-25 22:15:45 UTC  
> Updated at: 2022-01-04 16:32:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/132  

Hi, I have a serialization code for boost::multi_array, and related types that I would like to contribute to the code for example as `boost/serialization/multi_array.hpp".  Is it better if I contribute to Boost.MultiArray instead? That library doesn't seem to be actively maintained.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-25 22:37:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-433228652  

As a rule, serialization for a particular library is included in that library.  Only the data structures in std namespace are included in the serialization library itself.  
  
Congratulations: looks like multiArray has a new maintainer!

---

## Comment 2

> Username: alfC  
> Created at: 2018-10-25 22:51:51 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-433231561  

On Thu, Oct 25, 2018 at 3:37 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> As a rule, serialization for a particular library is included in that  
> library. Only the data structures in std namespace are included in the  
> serialization library itself.  
>  
good to know. (Hey! boost::array, boost::optional, and boost::variant are  
in boost/serialization/ :)  )  
  
> Congratulations: looks like multiArray has a new maintainer!  
>  
  
:) if you ask me, I would prefer to submit a new multi array library to  
boost: https://gitlab.com/correaa/boost-multi  
  
Boost.MultiArray has quite a lot of design flaws and limitations in my  
opinion. (of course it was great code at the time)  
  
Thank,  
A

---

## Comment 3

> Username: robertramey  
> Created at: 2019-02-24 18:55:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-466804953  

I'm sort of in a quandary here.  I'm getting push back from other library others who are concerned about adding dependencies to their libraries.  But I don't want to put them serialization library either.  For now I'm going to leave this open until I see a way to resolve this.

---

## Comment 4

> Username: correaa  
> Created at: 2019-02-24 19:33:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-466808753  

I share your concerns. I have the same dilemmas.  
  
I think the only good solution, if any at all, to this dilemma is to adopt  
(one of the many) multidimensional array concepts and support the concept  
at a basic level of optimization.  
  
Anyone caring about ultimate performance or specific features should be  
able to implement their own combination of multidimensional library and  
archive types, perhaps reusing or specializing the basic concept based  
code.  
  
  
On Sun, Feb 24, 2019, 10:55 Robert Ramey <notifications@github.com> wrote:  
  
> I'm sort of in a quandary here. I'm getting push back from other library  
> others who are concerned about adding dependencies to their libraries. But  
> I don't want to put them serialization library either. For now I'm going to  
> leave this open until I see a way to resolve this.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/132#issuecomment-466804953>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AC6BUyirRkaaAkg2DWMPM7MBr6qoVW4Aks5vQuAXgaJpZM4X7NVj>  
> .  
>

---

## Comment 5

> Username: g7uvw  
> Created at: 2019-07-30 19:15:25 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516555678  

Was there any progress on this, or is the code available anywhere outside of the boost repos? I'm trying to port a scientific image viewer over to using multi-array for it's internal logic. A nice way to load a flat binary file into a 3D array and then be able to write out views to other files would be very very useful.

---

## Comment 6

> Username: correaa  
> Created at: 2019-07-30 19:35:31 UTC  
> Updated at: 2019-07-30 19:36:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516562106  

@g7uvw, I ended up writing my own version of MultiArray, called Multi https://gitlab.com/correaa/boost-multi, and generic code is in `multi/utility.hpp` . This reminds me that I have to add the serialization utility, which should work for MultiArray as well.   
  
The only remaining obstacle is that Boost.Serialization forces me to depend on it to serialize to named tag archives (name value pair). I think Boost.Serialization should support "generic" name value pair so I don't have to have a hard dependency.

---

## Comment 7

> Username: correaa  
> Created at: 2019-07-30 19:39:13 UTC  
> Updated at: 2019-07-30 19:39:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516563345  

> As a rule, serialization for a particular library is included in that library. Only the data structures in std namespace are included in the serialization library itself.  
>   
> Congratulations: looks like multiArray has a new maintainer!  
  
@robertramey  BTW, John Kalb in Aspen suggested me to try to put the library  https://gitlab.com/correaa/boost-multi in the Boost Library incubator. I will do that when I find the time but suggestions for the preview are welcome (e.g. as issues in gitlab).

---

## Comment 8

> Username: robertramey  
> Created at: 2019-07-30 19:46:30 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516566504  

>The only remaining obstacle is that Boost.Serialization forces me to depend on it to serialize to named tag archives (name value pair). I think Boost.Serialization should support "generic" name value pair so I don't have to have a hard dependency.  
  
I don't think the serialization library forces you to use it's own name/value pair - maybe the xml archive does though.

---

## Comment 9

> Username: g7uvw  
> Created at: 2019-07-30 20:07:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516575576  

> @g7uvw, I ended up writing my own version of MultiArray, called Multi https://gitlab.com/correaa/boost-multi, and generic code is in `multi/utility.hpp` . This reminds me that I have to add the serialization utility, which should work for MultiArray as well.  
>   
  
I'm keen to try out the serialization utility if you make it available. I'll be using it with some multi-gigabyte 3 and 4 D datasets, so might provide some useful tests for you?

---

## Comment 10

> Username: correaa  
> Created at: 2019-07-30 20:16:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516578692  

> I don't think the serialization library forces you to use it's own name/value pair - maybe the xml archive does though.  
  
That is probably right, and still unfortunate because if I want my classes to be serializable with all the archives supported by Boost.Serialization I need to at least include `serialization/nvp.hpp`.   
  
The correct phrasing should be then, that it would be nice if Boost.Serialization archives could accept a generic named value pair type (e.g. anything with "name()->char*" and "value()" members and not specifically boost::serialization::  
  
Furthermore, I  think the archives should allow not-named serialization code and accept it, generating semi-automatic names, e.g. unique ids or type-based names or something like that.

---

## Comment 11

> Username: correaa  
> Created at: 2019-07-30 20:18:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516579488  

> > @g7uvw, I ended up writing my own version of MultiArray, called Multi https://gitlab.com/correaa/boost-multi, and generic code is in `multi/utility.hpp` . This reminds me that I have to add the serialization utility, which should work for MultiArray as well.  
>   
> I'm keen to try out the serialization utility if you make it available. I'll be using it with some multi-gigabyte 3 and 4 D datasets, so might provide some useful tests for you?  
  
Sure, although I am more interested in the specific use rather than in the size of the datasets. Yes, I am aware that speed is important and I know some basic techniques to tell Boost.Serialization how to serialize contiguous data of simple types if it is available.

---

## Comment 12

> Username: robertramey  
> Created at: 2019-07-30 22:01:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516612896  

here it is.  
  
xml_archive needs a name-value pair in order include the item name which xml requires.  This name_value pair is not any special type - it's just a serializable type which wraps another serializable type. I think you could use the xml_archive with your own name value type - I'm not sure though. You could also use  
  
typedef  boost::serialization::name_value_pair my_name_value_pair.  I think something like might also work.    
  
Basically you'll have to look a little more into the way xml_archive works.  I don't think it should be a big deal.  But then I don't think just using boost::serialization::name_value_pair would be a big deal either.

---

## Comment 13

> Username: correaa  
> Created at: 2019-07-31 03:37:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516680979  

Dear @robertramey   
  
I have to check again but I think it works like this. A non-intrusive serialization for given type, that is requiered to work with a tagged (named) archive (like xml_archive) needs something like this:  
  
```  
struct my_type{  
   int a;  
   double b;  
};  
```  
```  
template<class Archive>  
void serialize(Archive& ar, my_type& v){  
   ar & boost::serialization::make_nvp("a", v.a);  
   ar & boost::serialization::make_nvp("b", v.b);  
}  
```  
Unfortunately, this required to `#include<boost/serialization/nvp.hpp>`.  
I couldn't find a way to not use the specific type `boost::serialization::nvp`.  
That is, the serialization code now depends on Boost.Serialization. Although in principle this protocol could be used without Boost.Serialization at all (e.g. for other serialization library or other purposes).  
  
Notice how a serialization that doesn't need to work with XML, basically is completely independent of Boost.Serialization.  
  
```  
template<class Archive>  
void serialize(Archive& ar, my_type& v){  
   ar & v.a;  
   ar & v.b;  
}  
```

---

## Comment 14

> Username: g7uvw  
> Created at: 2019-07-31 09:30:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-516773422  

> > I'm keen to try out the serialization utility if you make it available. I'll be using it with some multi-gigabyte 3 and 4 D datasets, so might provide some useful tests for you?  
>   
> Sure, although I am more interested in the specific use rather than in the size of the datasets. Yes, I am aware that speed is important and I know some basic techniques to tell Boost.Serialization how to serialize contiguous data of simple types if it is available.  
  
In our CT lab we use an in-house file format. It's just a header, then a Z-stack of X by Y sized 8bit images. The existing software is very windows biased. I'm re-writing it in QT. I'm happy to discuss applications via email if that works for you. I don't want to derail this thread.

---

## Comment 15

> Username: correaa  
> Created at: 2021-12-01 11:13:38 UTC  
> Updated at: 2022-01-04 16:32:43 UTC  
> Url: https://github.com/boostorg/serialization/issues/132#issuecomment-983536778  

Hi @g7uvw   
  
I have finished serialization in the Multi library https://gitlab.com/correaa/boost-multi (not Boost.MultiArray).  
The reason it took me so long was because I wanted to add serialization compatibility without making my library have a hard dependency on Boost.Serialization. This was particularly challenging for compatibility of XML archive or any archive needing named-value pairs.  
  
But finally, I learned how to do it. Now the user of the Multi library depends on Boost.Serialization only if he wants to use it with Boost.Serialization archives.  
  
When dealing with arrays, as you said performance is very important so this are the timings if you are wondering, presumably relatives numbers are what matters. This is for storing a 4D array made of 64^4 = 16777216 random doubles, 134MB.  
  
| Archive format            | file size     | speed (read - write)          |  
| ---------------------------- | ------------- | ---------------------------------- |  
| XML                           | 662MB     |  11. MB/sec - 13. MB/sec  |  
| Text                            | 411MB     |  15. MB/sec - 16. MB/sec  |  
| Binary                        |  134MB    |   5.2 GB/sec  - 1.6 GB/sec |  
| XML (gzip on-the-fly) | 207 MB    |  8 MB/sec - 8 MB/sec         |  
  
The benchmark code can be found here: https://gitlab.com/correaa/boost-multi/-/blob/master/benchmark/serialization.cpp  
  
(The library also support the Cereal serialization library, if you are interested in the relative performance with Boost.Serialization see at the end of this section https://gitlab.com/correaa/boost-multi#serialization)  
  
For the XML archive format there is a strong trade off between structure and efficiency, if one wants a fully structured XML for the multiple dimensions there is a steep efficiency cost because fast contiguous dumping is lost just to print intermediate labels regularly.  
At the same time, if the arrays are very large or have a large dimensionality there is little readability gain by keeping this array structure.  
  
So what I did was to have multiple versions of serializations that control this.  
  
In Boost.Serialization version is just an `unsigned int`, so I am using this integer to decide when an array is large or small.  
For example if version is 16 (`#define MULTI_SERIALIZATION_ARRAY_VERSION 16`) then arrays with number of elements above 16 will be considered "large" an they will be dumped without structure (regardless of dimension).  
version 0 and -1 are special, 0 means all arrays are considered large, -1 (i.e. UINT_MAX) means that all arrays are considered small. By default, version is 0.   
  
The good part is that this compile-time choice can be changed and all versions are compatible. They do not affect the contents of Text and Binary (except the version tag), but they do affect the writing/reading speed. If this sound confusing the good news is that you don't have to worry about it.  
  
You can find a small example on how to use Multi + Boost.Serialization here:  
https://gitlab.com/correaa/boost-multi/-/blob/master/test/serialization.cpp#L105-128  
  
```cpp  
	multi::array<double, 2> arr({10, 10}, 0.);  
	std::iota(arr.data_elements(), arr.data_elements() + arr.num_elements(), 1000.);  
  
	std::stringstream ss;  
	{  
		boost::archive::xml_oarchive xoa{ss};  
		xoa<< BOOST_SERIALIZATION_NVP(arr);  
	}  
	multi::array<double, 2> arr2;  
	{  
		boost::archive::xml_iarchive xia{ss};  
		xia>> BOOST_SERIALIZATION_NVP(arr2);  
	}  
	BOOST_REQUIRE( extensions(arr2) == extensions(arr) );  
	BOOST_REQUIRE( arr2 == arr );  
```  
  
For reference, XML archive for "large" arrays looks like this:  
  
```xml  
<?xml version="1.0" encoding="UTF-8" standalone="yes" ?>  
<!DOCTYPE boost_serialization>  
<boost_serialization signature="serialization::archive" version="18">  
<arr class_id="0" tracking_level="0" version="0">  
        <extensions class_id="1" tracking_level="0" version="0">  
                <extension class_id="2" tracking_level="0" version="0">  
                        <first>0</first>  
                        <last>10</last>  
                </extension>  
                <extension>  
                        <first>0</first>  
                        <last>10</last>  
                </extension>  
        </extensions>  
        <elements>  
                <item>1.00000000000000000e+03</item>  
                <item>1.00100000000000000e+03</item>  
                ...  
        </elements>  
</arr>  
</boost_serialization>  
```  
  
Finally, I know that you have asked about serialization of Boost.MultiArray (not Multi), hopefully now that I understand the Serialization library better I can port this to Boost.MultiArray and also have feedback from experience.  
  
If for any reason you want to (upgrade?) to Multi, check it out here: https://gitlab.com/correaa/boost-multi, it is made so the transition from Boost.MultiArray is easy.
