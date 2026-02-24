# #103 - C++17: compilation errors if dispose function has noexcept specifier [Closed]

> Username: nkolotov  
> Created at: 2026-02-16 12:49:38 UTC  
> Updated at: 2026-02-17 08:16:00 UTC  
> Closed at: 2026-02-17 08:16:00 UTC  
> Url: https://github.com/boostorg/intrusive/issues/103  

This snippet compiles fine in C++11 or C++14 mode, but brakes after switching to C++17 or newer.  
C++17 makes `noexcept` specifier a part of function signature breaking traits used by `ebo_functor_holder`.  
```  
#include <boost/intrusive/avl_set.hpp>  
  
struct Val: boost::intrusive::avl_set_base_hook<>  
{  
	int m_int;  
  
	explicit Val(void) = delete;  
	explicit Val(Val const & other) = delete;  
	explicit Val(int i) noexcept : m_int{i} {}  
	  
	void operator =(Val const & other) = delete;  
    bool operator ==(Val const & other) const noexcept { return m_int == other.m_int; }  
    bool operator <(Val const & other) const noexcept { return m_int < other.m_int; }  
};  
  
using ValSet = boost::intrusive::avl_set<Val>;  
  
void dispose(Val * p_val) noexcept { delete p_val; }  
  
int main()  
{  
	ValSet set{};  
	try  
	{  
		set.insert(* new Val{1});  
		set.insert(* new Val{2});  
		set.insert(* new Val{3});  
	}  
	catch (...)  
	{}  
	set.clear_and_dispose(dispose);  
	return 0;  
}  
```  
With boost v1.90 and gcc15.2 `--std=c++17 -Wall -Wextra`  
```  
In file included from /app/boost/include/boost/intrusive/avltree_algorithms.hpp:24,  
                 from /app/boost/include/boost/intrusive/detail/avltree_node.hpp:27,  
                 from /app/boost/include/boost/intrusive/avl_set_hook.hpp:19,  
                 from /app/boost/include/boost/intrusive/avltree.hpp:21,  
                 from /app/boost/include/boost/intrusive/avl_set.hpp:17,  
                 from <source>:1:  
/app/boost/include/boost/intrusive/detail/ebo_functor_holder.hpp: In instantiation of 'class boost::intrusive::detail::ebo_functor_holder<void (*)(Val*) noexcept, void, false>':  
/app/boost/include/boost/intrusive/detail/node_cloner_disposer.hpp:75:8:   required from 'struct boost::intrusive::detail::node_disposer<void (*)(Val*) noexcept, boost::intrusive::bhtraits<Val, boost::intrusive::avltree_node_traits<void*, false>, boost::intrusive::safe_link, boost::intrusive::dft_tag, 5>, boost::intrusive::AvlTreeAlgorithms>'  
   75 | struct node_disposer  
      |        ^~~~~~~~~~~~~  
/app/boost/include/boost/intrusive/bstree.hpp:1600:20:   required from 'void boost::intrusive::bstree_impl<ValueTraits, VoidOrKeyOfValue, VoidOrKeyComp, SizeType, ConstantTimeSize, AlgoType, HeaderHolder>::clear_and_dispose(Disposer) [with Disposer = void (*)(Val*) noexcept; ValueTraits = boost::intrusive::bhtraits<Val, boost::intrusive::avltree_node_traits<void*, false>, boost::intrusive::safe_link, boost::intrusive::dft_tag, 5>; VoidOrKeyOfValue = void; VoidOrKeyComp = void; SizeType = long unsigned int; bool ConstantTimeSize = true; boost::intrusive::algo_types AlgoType = boost::intrusive::AvlTreeAlgorithms; HeaderHolder = void]'  
 1600 |          , detail::node_disposer<Disposer, value_traits, AlgoType>(disposer, &this->get_value_traits()));  
      |                    ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
<source>:32:23:   required from here  
   32 |         set.clear_and_dispose(dispose);  
      |         ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~  
/app/boost/include/boost/intrusive/detail/ebo_functor_holder.hpp:226:7: error: base type 'void (*)(Val*) noexcept' fails to be a struct or class type  
  226 | class ebo_functor_holder<T, Tag, false>  
      |       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
Compiler returned: 1  
```  
[online compiler](https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGe1wAyeAyYAHI%2BAEaYxCBmpAAOqAqETgwe3r56icmOAkEh4SxRMXF2mA6pQgRMxATpPn5ctpj2uQxVNQT5YZHRsbbVtfWZTQqDXcE9RX1mAJS2qF7EyOwc5gDMwcjeWADUJutuEahJBAD0wQTEXskAbphnTLe0APpKBAB0CPHxB9gmGgAggDAWNrg5dgA1MQgXbHU4gECXa53dggJ6vd4vCJMJQvBAnADWBzcfxBJgA7FYgQCAJyXXYsF6XA7U4E0jS0zCqeK0PDIQhQsQQW6oPDoWb7dYAEV2WHoBEwrPJnO5vP5guhtAgWt2aAYY32ZgAbLtUAQENFJQdZfLMIrlRyuTy%2BQKCELtQy8JKGKhuSt4u7YUyWVS8JTZZSrBTpSraXHReKzfFokwiMQpdKdWI9QJDeZTebLcRrTK5S17Ur1mzdrW4SdaMnU%2BnMzbs439fmTWaLVbcwb3b7/ZhA/sqbtiPalgxGczBK2y0Xoh8Q4JWWPY0C6/XPE3iGmSFK3O3%2B13C72S6fB37VAH3VGJ1PiDPV/fDj3iyu5wR1xHyTHHXZQEbmCYAPSEe1Mx3MZEWRED7kRDE3ntEktTJasVUBRN0DlPAFGyTATwAKl2eIXluMQfRvO8xwscsFUwUjyLEX8AI5QEGRYJhgggWZ/zZOktQg913ijP8MKdK4AE84zEp06XeD5giUWoIBIkIAHcPSjLgIz4iT2XjTlFOU6ICDU3ZNO0qkzD0wC6QU%2B0lINMyLKstCqXWOyDIctjDNEAhkAQXYIA%2BMK%2BPkqk/ydRTtkwGoXkMdAXnwfCkkI1KCP0gTOUnAhp12DR7L8jh5loTgAFZeD8DgtFIVBOFJSxrF2BRFmWRiNh4UgCE0Ur5kJEB1nWD5hrG8aJuNfROEkaq%2BvqzheAUEANB6vr5jgWAYEQFBUBYeI6GichKDQfbDpiYAuAquIaFoRViGWiAInmiJghqKTOG617mGIKSAHkIm0cpeu4XhTrYQQ/oYWgPtq3gsAiLxgDcMRaGW0HSCwLijHEOHMbwScKnudG6u5covEVT7eEuFp5r5CJ91%2BjwsHmq48BYKnSHuYhjiUaVMGx4A%2BSMda%2BAMYAFEhPBMA0v6Uxq7r%2BEEEQxHYKQZEERQVHUPHdCaAwRdMZrLH0PAImW2BmDYEB4TGLm%2Bm4WktFmeZUEDVJ0YAWk9sZ0BtI2rEsKRdk9gB1VGQ9D7kriYXhUG54hxUwC3eOaVpUhcBh3E8Bp/Cz7pCmKLIkhSARhkaBIS7aAvehiUYKwqAQOiGHORjT4HKnGGvpjrgZOnLvQxk6bui64eY2qWFYJDKyq5rxhqOF2VQAA5jU941JF2YBkGQXYro%2BMwQtwQhDy62ZeBB52BpACrVvKjhZtIDnb9IGq6oXpaVrWuGNu2iAkEWAQeIFNjoQFOgdegxBQisFWCvNeG8t47z3hVA%2BvBMD4HTOKPQSthCiHEOrHBWs1DzT1qQDS%2B54hUxnhwKqr95oLz%2BhTYB7pUBUCXqvdem9t6733ofCAHgzqQKNOsMeF91rzEtEwLAMRU730fs/Vab846LVsF/S%2B/VSCDWGqNCaujhpTXvusOe78VHqN/vATaO1wHnVAdYyBIBLrXT4HQe6j1np42%2Bu9TmnjfoAyBg4Tm4NGAEChjDeaCMkYo1oGjTmWNDBC1WHVfAhNHDE3mmTZAFNVjdRpvfOq9NGZSWZoki%2BicOYY25rzTA/NBbC1AD/MWTAJZSxlnLRgnMcEq3wdIQhShiG636AbYw1hrCm3NhYq27BbYEHtjER2V96ruzzJwb2vt/YjKDhSSOEcw7R33HHBOScU7jwbm0TO2cMgV0CJMQufQmjZFLmkVuFcHnVxubXQepzO792eZ89OTcu7vJ7oPcYA9RiAoKB8seCxJ5q2obQpRC1F5wM4YgnhKC%2BHHxbGfMRP9r7aL0Xo6aD9eAKLofPFRy1VpmNIJY/%2BIBAHMNsXtCB0RoHWw4CihB3DkGoNIOgk%2Bid0DYNkF0tWPTZBEJ1nVXQcRyFMEoaDeFxjlEcEYUAimZo2Fcq4Ug3hIUBGsozBsOYuKFmSOkZQah8ib6KPoZStR4jNFDRGoS3RxKjHkpMRwM1Gj75mBVUi31LtZnJGcJIIAA%3D%3D)
