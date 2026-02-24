# #329 - Serialization of a Derived Class from a Base Class Pointer is Incorrect in Boost 1.85 [Open]

> Username: ckreatsoMW  
> Created at: 2025-04-25 17:57:45 UTC  
> Updated at: 2025-05-20 15:34:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/329  

We have some code which is similar to [repro.zip](https://github.com/user-attachments/files/19914334/repro.zip).  
  
In Boost 1.81, the code successfully serializes and deserializes the Derived class from its Base pointer. However, in Boost 1.85, the deserialization fails when using a Text or Binary archive.  
  
  
We tracked down this issue to the one identified in the comments of https://github.com/boostorg/serialization/pull/287. If we manually revert this change and rerun the serialization/deserialization, the library behaves as expected.  
  
Boost 1.81 Text Serialization:  
```  
22 serialization::archive 19 1 1 1  
0 0 3 0 0 0 0 16 This is a string  
```  
  
Boost 1.85 Text Serialization:  
```  
22 serialization::archive 20 1 1 1  
0 0 3  
1 0 0 0 0 16 This is a string  
```  
  
As https://github.com/boostorg/serialization/pull/287 did not make a corresponding deserialization change, this seems to be a bug  
  
To reproduce this issue, do the following:  
1. Download [repro.zip](https://github.com/user-attachments/files/19914334/repro.zip)  
2. Build and run the "repro" executable and its dependencies with Boost 1.81. Everything passes as expected.  
3. Build and run the "repro" executable and its dependencies with Boost 1.85. The "derivedData" string fails to deserialize.

---

## Comment 1

> Username: tcormackMW  
> Created at: 2025-05-20 15:34:06 UTC  
> Url: https://github.com/boostorg/serialization/issues/329#issuecomment-2894894827  

Hi I am also running into this issue and am following up here to see if anyone had thoughts about this.
