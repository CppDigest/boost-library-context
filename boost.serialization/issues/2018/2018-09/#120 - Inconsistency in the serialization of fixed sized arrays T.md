# #120 - Inconsistency in the serialization of fixed sized arrays T[N] [Open]

> Username: correaa  
> Created at: 2018-09-25 02:33:53 UTC  
> Updated at: 2019-02-24 20:05:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/120  

I think there is an inconsistency in the library design, which is that the serialization of `T[N]` stores the value N in the archive. I think this violates some design principles of the library because it is stored to no purpose.   
  
The code is contained ultimately in `boost::archive::save_array_type::invoke` in the line `ar << BOOST_SERIALIZATION_NVP(count);`.   
Later the `count` is read in `boost::archive::load_array_type::invoke` but it is not part of the state of the object, it is only used as a "sanity" check (`if(static_cast<std::size_t>(count) > current_count) throw `...).  
This original code could be there for two reasons, to allow saving and loading to differently sized static arrays `T[N1]` and `T[N2]` respectively (when N2 >= N1), or as a runtime "type" check.   
  
But I think in both cases it violates the philosophy of Boost.Serialization by adding type information in the serialization instead of relying in static types and assuming correctness of compiled code.  
  
I detected this problem when trying to serialize `std::array<T, N>` to a custom archive and instead of getting `N` entries I got `N+1`, which was something that my custom archive relied on for it to be useful.  
  
A change like this (removing the `count` entry) for `T[N]` (and indirectly to `std::array` and `boost::array`) may require a breaking change or including some conditional code depending on the library version.  
  
A more pragmatic change could be to do this at the level of "true value types" such as a `std::array<T, N>` (I think B.S shouldn't worry to much about non-value types such T[N]),  and implement it as:  
  
```  
template <class Archive, class T, std::size_t N>  
void serialize(Archive& ar, std::array<T,N>& a, const unsigned int /* version */){  
    ar & boost::serialization::make_nvp(  
        "elems",  
        boost::serialization::make_array(a.data()), N) // was  *static_cast<T (*)[N]>(static_cast<void *>(a.data()))  
    );  
}  
```  
  
Finally a workaround, if one controls the archive type, is to specialize a couple of cases:  
  
```  
template<class It, class T, std::size_t N>  
numeric_oarchive<It>& operator<<(numeric_oarchive<It>& noa, std::array<T, N> const& a){  
	return noa & boost::serialization::make_array(a.data(), N);  
}  
template<class It, class T, std::size_t N>  
numeric_iarchive<It>& operator>>(numeric_iarchive<It>& nia, std::array<T, N>& a){  
	return nia & boost::serialization::make_array(a.data(), N);  
}  
```  
  
However then one must repeat this for `boost::array<T, N>` and `T[N]`.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-10-01 01:06:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/120#issuecomment-425766425  

You're right of course.  
  
The serialization of array is likely copied from or used the same (templated) code as the serialization of other collections.  I'm really disinclined to make any changes just to save the save/load of one integer.  It's just too much work for the benefit obtained.  
  
I'm impressed with your grasp of the library.  Don't be dissuaded from making contributions in the future.  
  
Robert Ramey

---

## Comment 2

> Username: correaa  
> Created at: 2018-10-01 04:20:32 UTC  
> Url: https://github.com/boostorg/serialization/issues/120#issuecomment-425785587  

On Sun, Sep 30, 2018 at 6:06 PM Robert Ramey <notifications@github.com>  
wrote:  
  
> You're right of course.  
>  
> The serialization of array is likely copied from or used the same  
> (templated) code as the serialization of other collections. I'm really  
> disinclined to make any changes just to save the save/load of one integer.  
> It's just too much work for the benefit obtained.  
>  
I bet it is a lot of work to maintain backward compatibility, but it has  
been done before (e.g. with `boost::shared_ptr`).  
  
> I'm impressed with your grasp of the library.  
>  
Well, even a past *failed* attempt to make a YAML archive many years ago  
made me know a lot of details about the internals of the library.  
  
> Don't be dissuaded from making contributions in the future.  
>  
Yes, I regret not being able to produce meaningful contributions, I have a  
hard time proposing actual tested code for a non-header-only library,  
because the testing is tricky.  
  
Thanks,  
A

---

## Comment 3

> Username: correaa  
> Created at: 2018-10-01 20:54:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/120#issuecomment-426059392  

BTW, another point of view is that `std::array<T, N>` should serialize like a `std::tuple<T, T, ...>` if consistency with other types is to be sought.

---

## Comment 4

> Username: robertramey  
> Created at: 2019-02-24 19:02:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/120#issuecomment-466805716  

>Yes, I regret not being able to produce meaningful contributions, I have a hard time proposing actual tested code for a non-header-only library, because the testing is tricky.  
  
If you can test the library on your machine, it's easy to add a new test.  But this entails hooking up with the whole boost infrastructure. It's a pain.  But once it's done, it's quite effective and convenient.

---

## Comment 5

> Username: alfC  
> Created at: 2019-02-24 20:05:35 UTC  
> Url: https://github.com/boostorg/serialization/issues/120#issuecomment-466811672  

I see, thank you. So, what is your recommendation? Downloading the boost  
sources, compile from scratch including tests and making new  
(serialization) tests?  
  
  
Bjam, what a pain!  
  
On Sun, Feb 24, 2019, 11:02 Robert Ramey <notifications@github.com> wrote:  
  
> Yes, I regret not being able to produce meaningful contributions, I have a  
> hard time proposing actual tested code for a non-header-only library,  
> because the testing is tricky.  
>  
> If you can test the library on your machine, it's easy to add a new test.  
> But this entails hooking up with the whole boost infrastructure. It's a  
> pain. But once it's done, it's quite effective and convenient.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/serialization/issues/120#issuecomment-466805716>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYBNhf7-xHs_sIDdyG8GNgLsp9MP_ks5vQuG3gaJpZM4W3wxy>  
> .  
>
