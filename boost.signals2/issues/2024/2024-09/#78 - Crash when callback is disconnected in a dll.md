# #78 - Crash when callback is disconnected in a dll [Open]

> Username: JassJam  
> Created at: 2024-09-06 23:03:26 UTC  
> Updated at: 2024-09-07 11:13:06 UTC  
> Url: https://github.com/boostorg/signals2/issues/78  

When using signals2 across multiple DDLs, everything works fine until the dlls gets unloaded, which results in crash the next time we dispatch the signal.  
I found this [stackoverflow post](https://stackoverflow.com/questions/28703432/access-violation-when-using-boostsignals2-and-unloading-dlls) which describes the same issue i'm having.  
  
The test sample:  
  
Shared.cpp:  
```CPP  
#include <boost/dll.hpp>  
#include <boost/signals2.hpp>  
#include <iostream>  
  
struct SomeInterface  
{  
    boost::signals2::signal<void()> signal;  
};  
  
boost::signals2::connection con;  
  
void init(SomeInterface* iface)  
{  
    con = iface->signal.connect([] { std::cout << "Hello, Shared!" << std::endl; });  
}  
  
void shutdown()  
{  
    con.disconnect();  
}  
  
BOOST_DLL_AUTO_ALIAS(init);  
BOOST_DLL_AUTO_ALIAS(shutdown);  
```  
  
Host.cpp:  
```CPP  
#include <boost/dll.hpp>  
#include <boost/signals2.hpp>  
#include <iostream>  
  
struct SomeInterface  
{  
    boost::signals2::signal<void()> signal;  
};  
  
int main()  
{  
    SomeInterface* iface = new SomeInterface;  
  
    {  
        boost::dll::shared_library dll("Shared", boost::dll::load_mode::append_decorations);  
  
        auto init     = dll.get<void (*)(SomeInterface*)>("init");  
        auto shutdown = dll.get<void (*)()>("shutdown");  
  
        init(iface);  
        iface->signal();  
        shutdown();  
    }  
  
    iface->signal();  
  
    return 0;  
}  
````

---

## Comment 1

> Username: fmhess  
> Created at: 2024-09-06 23:27:23 UTC  
> Url: https://github.com/boostorg/signals2/issues/78#issuecomment-2334934572  

I noticed you're not disconnecting the slot properly.  There is a  
disconnect method on the connection object.  
  
On Fri, Sep 6, 2024, 7:03 PM Yassine Sen ***@***.***> wrote:  
  
> When using signals2 across multiple DDLs, everything works fine until the  
> dlls gets unloaded, which results in crash the next time we dispatch the  
> signal.  
> I found this stackoverflow post  
> <https://stackoverflow.com/questions/28703432/access-violation-when-using-boostsignals2-and-unloading-dlls>  
> which describes the same issue i'm having.  
>  
> The test sample:  
>  
> Shared.cpp:  
>  
> #include <boost/dll.hpp>  
> #include <boost/signals2.hpp>  
> #include <iostream>  
> struct SomeInterface  
> {  
>     boost::signals2::signal<void()> signal;  
> };  
>  
> boost::signals2::connection con;  
> void init(SomeInterface* iface)  
> {  
>     con = iface->signal.connect([] { std::cout << "Hello, Shared!" << std::endl; });  
> }  
> void shutdown(SomeInterface* iface)  
> {  
>     iface->signal.disconnect(con);  
> }  
> BOOST_DLL_AUTO_ALIAS(init);BOOST_DLL_AUTO_ALIAS(shutdown);  
>  
> Host.cpp:  
>  
> #include <boost/dll.hpp>  
> #include <boost/signals2.hpp>  
> #include <iostream>  
> struct SomeInterface  
> {  
>     boost::signals2::signal<void()> signal;  
> };  
> int main()  
> {  
>     SomeInterface* iface = new SomeInterface;  
>  
>     iface->signal.connect([] { std::cout << "Hello, Host!" << std::endl; });  
>     {  
>         boost::dll::shared_library dll("Shared", boost::dll::load_mode::append_decorations);  
>  
>         auto init     = dll.get<void (*)(SomeInterface*)>("init");  
>         auto shutdown = dll.get<void (*)(SomeInterface*)>("shutdown");  
>  
>         init(iface);  
>         iface->signal();  
>         shutdown(iface);  
>     }  
>  
>     iface->signal();  
>  
>     return 0;  
> }  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/signals2/issues/78>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AARKTX5FGNHJXW5HSF7FQGDZVIYFHAVCNFSM6AAAAABNZNN6Q2VHI2DSMVQWIX3LMV43ASLTON2WKOZSGUYTCMRZHA2TSNQ>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: JassJam  
> Created at: 2024-09-06 23:36:16 UTC  
> Url: https://github.com/boostorg/signals2/issues/78#issuecomment-2334940123  

Updated the sample, thanks for pointing it out, however that was not the issue  
Anyway, i'll provide the stack trace.  
```  
Host.exe!boost::detail::sp_counted_base::release() Line 119	C++  
Host.exe!boost::detail::shared_count::~shared_count() Line 432	C++  
Host.exe!boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>::~shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>()	C++  
Host.exe!boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>::`scalar deleting destructor'(unsigned int)	C++  
Host.exe!std::destroy_at<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>(boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>> * const _Location) Line 306	C++  
Host.exe!std::_Default_allocator_traits<std::allocator<std::_List_node<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,void *>>>::destroy<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>(std::allocator<std::_List_node<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,void *>> & __formal, boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>> * const _Ptr) Line 707	C++  
Host.exe!std::_List_node<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,void *>::_Freenode<std::allocator<std::_List_node<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,void *>>>(std::allocator<std::_List_node<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,void *>> & _Al, std::_List_node<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,void *> * _Ptr) Line 317	C++  
Host.exe!std::list<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>,std::allocator<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>>::erase(const std::_List_const_iterator<std::_List_val<std::_List_simple_types<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>>> _Where) Line 1417	C++  
Host.exe!boost::signals2::detail::grouped_list<int,std::less<int>,boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>::erase(const std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>> & key, const std::_List_iterator<std::_List_val<std::_List_simple_types<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>>> & it) Line 165	C++  
Host.exe!boost::signals2::detail::signal_impl<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::nolock_cleanup_connections_from(boost::signals2::detail::garbage_collecting_lock<boost::signals2::mutex> & lock, bool grab_tracked, const std::_List_iterator<std::_List_val<std::_List_simple_types<boost::shared_ptr<boost::signals2::detail::connection_body<std::pair<enum boost::signals2::detail::slot_meta_group,boost::optional<int>>,boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>>,boost::signals2::mutex>>>>> & begin, unsigned int count) Line 453	C++  
Host.exe!boost::signals2::detail::signal_impl<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::nolock_cleanup_connections(boost::signals2::detail::garbage_collecting_lock<boost::signals2::mutex> & lock, bool grab_tracked, unsigned int count) Line 474	C++  
Host.exe!boost::signals2::detail::signal_impl<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::nolock_force_unique_connection_list(boost::signals2::detail::garbage_collecting_lock<boost::signals2::mutex> & lock) Line 489	C++  
Host.exe!boost::signals2::detail::signal_impl<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::create_new_connection(boost::signals2::detail::garbage_collecting_lock<boost::signals2::mutex> & lock, const boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>> & slot) Line 516	C++  
Host.exe!boost::signals2::detail::signal_impl<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::nolock_connect(boost::signals2::detail::garbage_collecting_lock<boost::signals2::mutex> & lock, const boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>> & slot, boost::signals2::connect_position position) Line 560	C++  
Host.exe!boost::signals2::detail::signal_impl<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::connect(const boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>> & slot, boost::signals2::connect_position position) Line 168	C++  
Host.exe!boost::signals2::signal<void __cdecl(void),boost::signals2::optional_last_value<void>,int,std::less<int>,boost::function<void __cdecl(void)>,boost::function<void __cdecl(boost::signals2::connection const &)>,boost::signals2::mutex>::connect(const boost::signals2::slot<void __cdecl(void),boost::function<void __cdecl(void)>> & slot, boost::signals2::connect_position position) Line 699	C++  
Host.exe!main() Line 25	C++  
```

---

## Comment 3

> Username: JassJam  
> Created at: 2024-09-07 11:13:06 UTC  
> Url: https://github.com/boostorg/signals2/issues/78#issuecomment-2335155110  

As stated on the [stackoverflow post](https://stackoverflow.com/questions/28703432/access-violation-when-using-boostsignals2-and-unloading-dlls), one could wrap the signal around an interface to delegate the connect() method to the owner of the signal, however a fix to this issue would be nicer than the extra indirection for a workaround.  
  
```CPP  
  
    using Connection       = boost::signals2::connection;  
    using ScopedConnection = boost::signals2::scoped_connection;  
  
    template<typename... SigTy> class Signal  
    {  
    public:  
        using base_type = boost::signals2::signal<SigTy...>;  
        using slot_type = typename base_type::slot_type;  
        using slot_ex   = typename base_type::extended_slot_type;  
  
    private:  
        struct ISignal  
        {  
            virtual ~ISignal()                                = default;  
            virtual Connection Connect(const slot_type& slot) = 0;  
            virtual Connection ConnectEx(const slot_ex& slot) = 0;  
        };  
  
        struct SignalImpl : ISignal  
        {  
            [[nodiscard]] Connection Connect(const slot_type& slot) override  
            {  
                return m_Signal.connect(slot);  
            }  
            [[nodiscard]] Connection ConnectEx(const slot_ex& slot) override  
            {  
                return m_Signal.connect_extended(slot);  
            }  
            base_type m_Signal;  
        };  
  
    public:  
        Connection Connect(const slot_type& slot)  
        {  
            return m_Signal->Connect(slot);  
        }  
  
        Connection ConnectEx(const slot_ex& slot)  
        {  
            return m_Signal->ConnectEx(slot);  
        }  
  
        template<typename... Args> auto Invoke(Args&&... args)  
        {  
            auto impl = static_cast<SignalImpl*>(m_Signal.get());  
            return impl->m_Signal(std::forward<Args>(args)...);  
        }  
  
    private:  
        std::unique_ptr<ISignal> m_Signal = std::make_unique<SignalImpl>();  
    };  
```
