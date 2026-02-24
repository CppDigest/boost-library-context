# #287 Use the first instance of bos during serialization [Merged]

> Username: olologin  
> Created at: 2023-07-20 13:43:56 UTC  
> Updated at: 2025-04-25 17:59:11 UTC  
> Merged at: 2023-09-01 22:14:13 UTC  
> Closed at: 2023-09-01 22:14:13 UTC  
> Url: https://github.com/boostorg/serialization/pull/287  

There are situations when we can have multiple instances of basic_oserializer for the same type participating in serialization to the same archive. The issues start when these multiple instances have different object tracking settings, because in one executable you never serialize some type by pointer, and in another dll you serialized the type by pointer only once, and the second dll is now trying to store everything with object-tracking enabled. And if you serialized the same object into an archive twice but with both libraries you will not be able to correctly deserialize such an archive, you will end up seeing some random failure or "unregistered class exception", basically it is UB from this point.  
  
The reading side in basic_iarchive.cpp always uses the first object tracking settings for a type it registers (in cobject_id_vector). So we should have the same logic in basic_oarchive.cpp.  
  
Please check if this change is harmless, unfortunately it is not easy for me to extract reproducible example for this issue from our code, because it requires multiple modules.

---

## Comment 1

> Username: robertramey  
> Created_at: 2023-08-24 17:54:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-1692167601  

you've hit on a fundamental mistake in the library.  I only discovered this a couple of years ago.  The shows up when one uses the code in different builds.  Like when one builds a library and then uses it in an executable.  I didn't mess with it because no one complained.  And fixing would be somewhat painful, given that one had to take into account archives which were already created - perhaps decades ago.  (welcome to the world of Boost library development).  I'll take a careful look at you fix.  I'm sort of suspecting that you might not have been "thinking big" enough as it's very hard to do when one is addressing a specific problem.    
  
Then I'll either merge or make my own fix inspired by your example. It will likely require consider the serialization library version, encoded in the header.  Of course if you want to invest more effort into this I would encourage it.  In a sense it's low priority.  But it IS a design error.  It has been my goal over the last 20 years to maintain a state of "no know bugs" in an effort to arrive at "no bugs".  Of course the final arrival is never provable.  As you might see, I'm kind of obsessive about these details.  
  
Thanks so much for spending time to chase this down. Hopefully your efforts will not be in vain.  
  
Robert Ramey

---

## Comment 2

> Username: olologin  
> Created_at: 2023-08-27 17:25:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-1694720090  

@robertramey   
Thank you too for responding. Do as you want. This fix is definitely fixing issue on our codebase.

---

## Comment 3

> Username: hajokirchhoff  
> Created_at: 2025-02-20 12:19:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-2671337108  

Hi folks, unfortunately this pull request breaks our application. Files saved with this version can no longer be loaded. We reverted these changes and now our application can save and load files again.

---

## Comment 4

> Username: olologin  
> Created_at: 2025-02-20 12:41:43 UTC  
> Updated_at: 2025-02-20 13:21:47 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-2671385455  

> Hi folks, unfortunately this pull request breaks our application. Files saved with this version can no longer be loaded. We reverted these changes and now our application can save and load files again.  
  
Could you maybe provide some details? Like platform, sample for reproduction? We use this in production on quite complex build structures and everything works without problems.  
  
Maybe you have troubles because some archive settings are different for serialized types during writing and reading? This can happen in different DLLs (if you save from one DLL, and read from another, or if you write into the same archive from two dlls). For example object tracking flag can be different, maybe some other flag. It could be that previous version worked for you accidentally, and now incorrect code has problems with correct serialization.

---

## Comment 5

> Username: hajokirchhoff  
> Created_at: 2025-02-20 18:50:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-2672390316  

Thanks for the fast reply.   
  
My team is working on finding a minimal sample. Our application has a large codebase and we've been using serialization since boost 1.33. This is the first time we are seeing something like this.  
  
We serialize a vector of "complex object". Inside the "complex object" is another vector with boost::shared_ptrs<some_base_class>.  
  
We have "always_track" for "some_base_class".  
  
The weird thing is: if we save a file with two "complex objects" in the vector, things work fine. But if we add a third "complex object" and save it, we get an exception when loading "cannot downcast" (I'll get the exact exception tomorrow). This happens because the load mechanism for the boost::shared_ptr<some_base_class> tries to deduct the actual type of the object pointed to by "some_base_class".  
  
For some odd reason, the down cast works fine for the first two instances, but fails for the third instance.  
  
I'll follow up on your hint regarding different archive settings. We do have several different DLLs, but afaik the saving/loading is done from within the same dll. There is only one place for save/load in the entire application. I will check this.

---

## Comment 6

> Username: hajokirchhoff  
> Created_at: 2025-02-21 15:31:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-2674860440  

@olologin , I hope you can help me understand the problem better. Your guess seems to be correct:  
> Maybe you have troubles because some archive settings are different [...] if you write into the same archive from two dlls).  
  
I am serializing a shared pointer to class `B` which inherits from class `A` and the classes are indeed located in different DLLs.  
This is Windows 11, Visual Studio 2022.  
  
I diffed two text archives. Without the patch (i.e. the old version) you see object_id="_7", _even_ though tracking_level is set to "0", which surprises me.   
```  
		<A class_id="10" tracking_level="0" version="0" object_id="_7">  
```  
With your PR the object_id is omitted and the object is indeed not counted.  
```  
		<A class_id="10" tracking_level="0" version="0">  
```  
Your PR changes exactly this line where the tracking level is queried: https://github.com/boostorg/serialization/blob/8a8c62864f05732131bf6785d54466d8ac6cd477/src/basic_oarchive.cpp#L265  
  
  
  
Question: Am I correct that `object_id` should only be present when `tracking_level` is set to 1? Then the old file would actually seem broken. Also, class A is the only class for which tracking_level is set to 0 but object_ids are generated. In all other cases, object_ids are only generated for classes with tracking_level set to 1.  
  
So it would look like to me as if in our case `bos.tracking(m_flags)` returns true (even though tracking_level="0"), but `co.m_bos_ptr->tracking(m_flags)` returns false.  
  
Any idea what I should be looking for? We are using a couple of BOOST_CLASS_TRACKING(track_always), but not for class B or class A. Could "tracking(m_flags)" return different values depending on which DLL the template was instantiated/linked to?

---

## Comment 7

> Username: ckreatsoMW  
> Created_at: 2025-04-25 17:59:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/287#issuecomment-2831073680  

@hajokirchhoff @olologin We are also seeing this issue as a result of this change. I created https://github.com/boostorg/serialization/issues/329 to track this

---
