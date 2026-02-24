# #68 - unresolved external symbol BCrypt*** after upgrading to boost 1.67 [Closed]

> Username: daef  
> Created at: 2018-05-07 12:57:23 UTC  
> Updated at: 2024-06-14 01:21:24 UTC  
> Closed at: 2018-08-01 13:29:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/68  

After upgrading boost from 1.66 to 1.67 my code  
  
    auto id = boost::lexical_cast<std::string>(boost::uuids::random_generator()());  
  
doesn't compile anymore.  
  
    error LNK2019: unresolved external symbol BCryptCloseAlgorithmProvider referenced in function "public: __cdecl boost::uuids::random_generator_pure::~random_generator_pure(void)" (??1random_generator_pure@uuids@boost@@QEAA@XZ)  
    error LNK2001: unresolved external symbol BCryptCloseAlgorithmProvider  
    error LNK2019: unresolved external symbol BCryptGenRandom referenced in function "public: struct boost::uuids::uuid __cdecl boost::uuids::random_generator_pure::operator()(void)" (??Rrandom_generator_pure@uuids@boost@@QEAA?AUuuid@12@XZ)  
    error LNK2001: unresolved external symbol BCryptGenRandom  
    error LNK2019: unresolved external symbol BCryptOpenAlgorithmProvider referenced in function "public: __cdecl boost::uuids::random_generator_pure::random_generator_pure(void)" (??0random_generator_pure@uuids@boost@@QEAA@XZ)  
    error LNK2001: unresolved external symbol BCryptOpenAlgorithmProvider  
    fatal error LNK1120: 3 unresolved externals  
    build stopped: subcommand failed.  
  
In my header I'm including  
  
    #include <boost/format.hpp>  
    #include <boost/lexical_cast.hpp>  
    #include <boost/uuid/uuid.hpp>  
    #include <boost/uuid/uuid_generators.hpp>  
    #include <boost/uuid/uuid_io.hpp>  
    #include <boost/algorithm/string/predicate.hpp>  
  
Installed boost via vcpkg, building using ninja/msbuild'17  
using boost with cmake:  
  
    find_package(Boost COMPONENTS system  
                              serialization  
                              thread  
                              date_time  
                              regex  
                              random  
                              program_options  
                              filesystem      REQUIRED)  
  
  
Do I have to link against additional libraries?  
  
Pinging @jeking3 because https://www.boost.org/doc/libs/1_67_0/boost/uuid/detail/random_provider_bcrypt.ipp looks somewhat related.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-05-07 13:17:13 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-387061372  

@daef You need to link with `bcrypt.lib` from Windows SDK.  
  
https://msdn.microsoft.com/en-us/library/windows/desktop/aa375377(v=vs.85).aspx  
  
@jeking3 Maybe we could add a `#pragma comment` for MSVC to link the library automatically?

---

## Comment 2

> Username: daef  
> Created at: 2018-05-07 13:56:28 UTC  
> Updated at: 2018-05-07 13:56:38 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-387072720  

Thanks @Lastique - that did the trick. If there's an update on the `#pragma comment` I'd happily remove the explicit reference thou :)

---

## Comment 3

> Username: jeking3  
> Created at: 2018-05-07 22:20:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-387224628  

It is supposed to autolink bcrypt.lib unless you disable that boost  
feature.  Is that the case?  
  
On Mon, May 7, 2018, 9:56 AM mnml_ <notifications@github.com> wrote:  
  
> Thanks @Lastique <https://github.com/Lastique> - that did the trick. If  
> there's an update on the #pragma comment I'll happily remove the explicit  
> reference thou :)  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/uuid/issues/68#issuecomment-387072720>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbYBfpiMO4rnE3voEWzScPgmN9xUqks5twFKNgaJpZM4T07bo>  
> .  
>

---

## Comment 4

> Username: jeking3  
> Created at: 2018-05-08 17:58:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-387489129  

My guess is that your project uses BOOST_ALL_NO_LIB and therefore you need to add bcrypt.lib to your library list manually.  If that's not the case please let me know @daef - thanks.

---

## Comment 5

> Username: daef  
> Created at: 2018-05-15 11:37:37 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-389135749  

The only thing I use is BOOST_ALL_DYN_LINK

---

## Comment 6

> Username: jeking3  
> Created at: 2018-05-15 15:20:25 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-389206492  

Then it should link automatically based on the code at https://github.com/boostorg/uuid/blob/develop/include/boost/uuid/detail/random_provider_bcrypt.ipp#L17.  I'll reopen for now.

---

## Comment 7

> Username: jeking3  
> Created at: 2018-08-01 13:29:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-409574630  

We haven't seen any further reports of this, so I suspect it was environmental.  I am going to close this out.

---

## Comment 8

> Username: pkoller  
> Created at: 2018-10-15 12:38:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-429837014  

I just had the same issue

---

## Comment 9

> Username: ras0219-msft  
> Created at: 2018-10-16 09:44:57 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-430173245  

This is caused by vcpkg disabling the boost autolink feature (because the libraries are installed with names incompatible with boost's autolink).  
  
I'll see how we can resolve this downstream in vcpkg with https://github.com/Microsoft/vcpkg/issues/4481.

---

## Comment 10

> Username: Lastique  
> Created at: 2018-10-16 10:17:06 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-430183055  

@ras0219-msft Just wanted to point you to the documentation of macros recognized by autolink infrastructure:  
  
https://github.com/boostorg/config/blob/develop/include/boost/config/auto_link.hpp#L15  
  
For example, if your installed libraries simply omit mangling, you may be able to just define `BOOST_AUTO_LINK_NOMANGLE` instead of disabling autolinking completely.

---

## Comment 11

> Username: sagarjha  
> Created at: 2019-06-19 22:12:57 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-503766020  

I had the same issue with `vcpkg` messing up boost's linking.  
I fixed it by defining `BOOST_UUID_FORCE_AUTO_LINK` by using the compiler options.

---

## Comment 12

> Username: pmahend1  
> Created at: 2019-07-18 17:45:33 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-512918249  

Adding bcrypt.lib for additional dependencies solved it  
  
![image](https://user-images.githubusercontent.com/21352870/61479667-4caff580-a962-11e9-9ede-c1d9fea5c701.png)

---

## Comment 13

> Username: mathstuf  
> Created at: 2019-11-05 19:39:33 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-549985937  

Hmm. The CMake targets should probably export this information (rather than relying on autolinking). Cc: @pdimov

---

## Comment 14

> Username: Be-ing  
> Created at: 2022-07-12 03:06:16 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-1181262343  

> We haven't seen any further reports of this, so I suspect it was environmental. I am going to close this out.  
  
I am also encountering this with Boost 1.72 on Windows (not using vcpkg) using `find_package` with the Boost CMake config file. As far as I can tell there haven't been any changes to fix this.

---

## Comment 15

> Username: pdimov  
> Created at: 2022-07-12 07:20:20 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-1181407855  

I think that this has been resolved in Boost 1.78 (https://github.com/boostorg/boost_install/issues/54) by no longer setting BOOST_ALL_NO_LIB in the CMake configuration files.

---

## Comment 16

> Username: natanaelece  
> Created at: 2024-06-13 19:27:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-2166612892  

Estou usando Visual Studio 2022, após instalar a biblioteca pelo vcpkg e adicionar:  
#include <cryptopp/sha.h>  
#include <cryptopp/filters.h>  
#include <cryptopp/hex.h>  
#pragma comment(lib, "cryptopp")  
  
Recebo os erros:  
Gravidade	Código	Descrição	Projeto	Arquivo	Linha	Estado de Supressão	Detalhes  
Erro	LNK1120	3 externo não resolvidos	vLite Project	D:\SRV_WYD\vLite_Project\Aelion\dbg.dll	1		  
Erro	LNK2001	símbolo externo não resolvido _BCryptCloseAlgorithmProvider@8	vLite Project	D:\SRV_WYD\vLite_Project\Code\cryptopp.lib(osrng.cpp.obj)	1		  
Erro	LNK2001	símbolo externo não resolvido _BCryptGenRandom@16	vLite Project	D:\SRV_WYD\vLite_Project\Code\cryptopp.lib(osrng.cpp.obj)	1		  
Erro	LNK2001	símbolo externo não resolvido _BCryptOpenAlgorithmProvider@16	vLite Project	D:\SRV_WYD\vLite_Project\Code\cryptopp.lib(osrng.cpp.obj)	1		  
  
Adicionei bcrypt.lib as dependências do projeto, sem sucesso.  
  
Alguém pode me ajudar?

---

## Comment 17

> Username: Lastique  
> Created at: 2024-06-13 20:27:44 UTC  
> Updated at: 2024-06-14 01:21:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/68#issuecomment-2166706242  

@eduardo-eller This doesn't seem to be related to Boost.UUID, you're not even including its headers. Please report this to cryptopp developers.  
  
If I had to guess then you or cryptopp are probably missing `extern "C"` around bcrypt function declarations or something. E.g. `_BCryptCloseAlgorithmProvider@8` looks like improperly mangled.  
  
PS: Please, make your future comments in English.
