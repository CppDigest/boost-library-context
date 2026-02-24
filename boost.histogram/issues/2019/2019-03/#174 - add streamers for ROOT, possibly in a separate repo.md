# #174 - add streamers for ROOT, possibly in a separate repo [Open]

> Username: HDembinski  
> Created at: 2019-03-19 20:37:02 UTC  
> Updated at: 2019-04-26 17:23:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/174  

It would be great if one could   
- deserialize native ROOT histograms into equivalent Boost::Histograms  
- serialize Boost::Histograms with a native ROOT histogram equivalent as this native equivalent  
- serialize Boost::Histograms without a native ROOT histogram equivalent into ROOT primitives  
  
This would help people who want to do the transition a lot.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-03-19 20:38:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/174#issuecomment-474574669  

If this requires ROOT as a dependency (probably does), then these streamers could be implemented in a separate repository.

---

## Comment 2

> Username: jpivarski  
> Created at: 2019-03-20 00:00:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/174#issuecomment-474630806  

This doesn't sound like a serialization feature but a conversion one. Aren't you talking about converting Boost Histogram objects into the equivalent ROOT objects, serializing normally, deserializing normally, and converting ROOT objects into Boost Histogram objects?  
  
(Mixing conversion and serialization, such that no ROOT objects exist in memory at any time, would not have an effect observable to the user and would only complicate both.)  
  
Or do I misunderstand?

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-03-20 07:30:45 UTC  
> Updated at: 2019-03-20 07:32:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/174#issuecomment-474717925  

My idea was indeed to mix conversion and serialization as you put it. There should be no conversion in memory. We should be able to read the binary format of a native ROOT histogram and directly create the corresponding in memory representation of a Boost::Histogram. We want to avoid the extra step of creating the native ROOT histogram in memory. This is more efficient, for one, and it avoid the problems with the automatic memory management that you have with ROOT histograms. There is an observable difference for the user, namely that using Boost::Histograms in this way will not generate random crashes, which are about to get with normal ROOT histograms, if you do not follow the implicit and bad rules about memory management laid out by ROOT by the letter.  
  
(De)serialization is already a kind of conversion - from a memory representation to a binary representation on disk. We should be able to hack this, by circumventing the standard ROOT streaming facilities to create the desired effect. Like you do, Jim, when you read histograms in uproot.

---

## Comment 4

> Username: jpivarski  
> Created at: 2019-03-20 11:24:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/174#issuecomment-474789787  

In uproot, it's a deserialization followed by a conversion, or a conversion followed by a serialization, if you go the other way. However, there are no issues with ROOT's memory model because ROOT it's not invoked. Keeping the serialization and conversion separate allows for more flexibility—there's a "canonical form" that's close to the streamer and therefore handled automatically (not circumvented), and all the other forms are converted to and from that canonical form. This is similar to the role that Aghast will play—a single target for all conversions so that we don't need as much conversion code, though serialization to ROOT would still involve another conversion to the form that mirrors the streamer.  
  
To do the same in C++ would require us to be able to intercept the mechanism that writes bytes into ROOT file without going through the process of making the ROOT objects. I don't see how the existence of code to do this in Python is a step toward being able to do it within the ROOT framework. The methods we'd need to subvert might be private. Doing it outside the ROOT framework but in C++ means writing a C++ uproot.  
  
That's not impossible: [this project] (https://github.com/PerilousApricot/spark-ttree) is a Java uproot, but it might be more ambitious than you're thinking.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-03-20 22:11:39 UTC  
> Updated at: 2019-03-20 22:12:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/174#issuecomment-475048461  

Ok, thank you for this valuable input. We don't want to replicate uproot in C++, that sounds like a lot of work. Perhaps it is enough for people to be able to read/write ROOT histograms in Python, then we could build on uproot and Aghast. Basically we get the same effect and people should use Python anyway instead of C++ for analysis.

---

## Comment 6

> Username: jpivarski  
> Created at: 2019-03-21 01:17:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/174#issuecomment-475085175  

Okay, well if that's the case, this is something that we wanted to do anyway: Boost ←→ Aghast ←→ anything.  
  
Apart from ROOT, there are reasons we might want to convert Boost directly into Aghast Flatbuffers in C++. We want to use Boost to accumulate histograms in the worker nodes of the IRIS-HEP query service, serialized and aggregates them, and return Aghast objects. Going directly from Boost to Aghast in C++ would be a benefit.  
  
At the moment, though, we're still figuring out the overall plan.
