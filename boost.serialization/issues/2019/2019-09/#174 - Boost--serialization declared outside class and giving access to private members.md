# #174 - Boost::serialization declared outside class and giving access to private members [Closed]

> Username: rachelglenn  
> Created at: 2019-09-16 13:05:19 UTC  
> Updated at: 2019-10-19 16:09:52 UTC  
> Closed at: 2019-10-19 16:09:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/174  

Here is what I am trying to do:  
  
struct Data{  
 double variable1;  
 double variable2;  
};  
  
Class A {  
private:  
  friend boost::serialization::access;  
   boost::shared_ptr<data> pData;  
public:  
   double getVariable1(){  
   return pData->variable1;  
}  
};  
  
  
template<class Archive>  
void boost::serialization::serialize(Archive & ar, A& aIn, const unsigned int version)  
{  
ar & aIn.pData->varialble1;  
}  
  
I get the the error that I am trying to access a private data member  
  
I have also tried   
  
template<class Archive>  
void boost::serialization::serialize(Archive & ar, A& aIn, const unsigned int version)  
{  
ar & aIn.getDouble1();  
}  
  
I get the error that serialize is ambiguous. Any ideas? Thank you in advance.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-09-20 19:15:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-533677699  

try   
template  
void boost::serialization::serialize(Archive & ar, A& aIn, const unsigned int version)  
{  
ar & aln.pData;  
}

---

## Comment 2

> Username: correaa  
> Created at: 2019-09-20 19:29:20 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-533681459  

> try  
> template  
> void boost::serialization::serialize(Archive & ar, A& aIn, const unsigned int version)  
> {  
> ar & aln.pData;  
> }  
  
Is this complete code or Github ate the formatting?

---

## Comment 3

> Username: robertramey  
> Created at: 2019-09-20 19:47:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-533686500  

it's the complete code.  you might have to include boost/serialization/shared_ptr.hpp .  Boost serialization has intrinsic support for smart pointers.

---

## Comment 4

> Username: correaa  
> Created at: 2019-09-20 19:56:31 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-533689281  

ok, I didn't understand the syntax, I think that there are some angle brackets missing.

---

## Comment 5

> Username: robertramey  
> Created at: 2019-09-20 20:22:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-533696381  

I don't think so.

---

## Comment 6

> Username: robertramey  
> Created at: 2019-10-19 15:54:45 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-544163999  

may I presume that this is fixed.  I want to close it.

---

## Comment 7

> Username: robertramey  
> Created at: 2019-10-19 16:09:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/174#issuecomment-544166090  

Feel free to re-open it if you dig up more information.
