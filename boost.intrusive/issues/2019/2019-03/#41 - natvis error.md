# #41 - natvis error [Closed]

> Username: vinniefalco  
> Created at: 2019-03-12 20:49:00 UTC  
> Updated at: 2019-03-13 22:00:51 UTC  
> Closed at: 2019-03-13 22:00:51 UTC  
> Url: https://github.com/boostorg/intrusive/issues/41  

It looks like an intrusive structure was changed, but the .natvis file was not updated:  
  
Natvis: C:\USERS\VINNIE\APPDATA\LOCAL\MICROSOFT\VISUALSTUDIO\15.0_9E383727\EXTENSIONS\2LQQCEYY.5SD\Visualizers\boost_IntrusiveContainers.natvis(48,29): Error: class "boost::intrusive::list_impl<boost::intrusive::bhtraits<boost::beast::http::basic_fields<std::allocator<char> >::element,boost::intrusive::list_node_traits<void *>,0,boost::intrusive::dft_tag,1>,unsigned int,0,void>::root_plus_size" has no member "size_"  
    Error while evaluating 'data_.root_plus_size_.size_' in the context of type 'lounge-server.exe!boost::intrusive::list_impl<boost::intrusive::bhtraits<boost::beast::http::basic_fields<std::allocator<char> >::element,boost::intrusive::list_node_traits<void *>,0,boost::intrusive::dft_tag,1>,unsigned int,0,void>'.  
Natvis: C:\USERS\VINNIE\APPDATA\LOCAL\MICROSOFT\VISUALSTUDIO\15.0_9E383727\EXTENSIONS\2LQQCEYY.5SD\Visualizers\boost_IntrusiveContainers.natvis(35,29): Error: class "boost::intrusive::list_impl<boost::intrusive::bhtraits<boost::beast::http::basic_fields<std::allocator<char> >::element,boost::intrusive::list_node_traits<void *>,0,boost::intrusive::dft_tag,1>,unsigned int,0,void>::root_plus_size" has no member "size_"  
    Error while evaluating 'data_.root_plus_size_.size_' in the context of type 'lounge-server.exe!boost::intrusive::list_impl<boost::intrusive::bhtraits<boost::beast::http::basic_fields<std::allocator<char> >::element,boost::intrusive::list_node_traits<void *>,0,boost::intrusive::dft_tag,1>,unsigned int,0,void>'.  
The program '[23136] lounge-server.exe' has exited with code 0 (0x0).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-03-13 21:33:12 UTC  
> Url: https://github.com/boostorg/intrusive/issues/41#issuecomment-472614487  

The module does not contain any natvis file. What's the error report?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-03-13 21:59:55 UTC  
> Url: https://github.com/boostorg/intrusive/issues/41#issuecomment-472622889  

Ahhh my bad, I must have installed them from the visual studio marketplace, or here:  
https://github.com/KindDragon/CPPDebuggerVisualizers

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-13 22:00:51 UTC  
> Url: https://github.com/boostorg/intrusive/issues/41#issuecomment-472623191  

You might consider including a .natvis file for intrusive it is (well.. "was") quite handy. And I wrote one for my JSON class which turned out to be quite handy, Visual Studio picks it up automatically. I'll close this since it isn't relevant.
