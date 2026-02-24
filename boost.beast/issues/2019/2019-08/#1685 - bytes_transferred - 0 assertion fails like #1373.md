# #1685 - bytes_transferred > 0 assertion fails  like #1373 [Closed]

> Username: xiongguangjie  
> Created at: 2019-08-22 06:59:29 UTC  
> Updated at: 2019-10-03 09:16:24 UTC  
> Closed at: 2019-10-03 09:16:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1685  

bytes_transferred > 0 assertion fails like https://github.com/boostorg/beast/issues/1373   
assert at boost\libs\beast\include\boost\beast\websocket\impl\read.ipp:497  
  
but i use `stream<ip::tcp::socket>` ， rarely ocurr  
beast version:  
189-hf1 (boost-1.69.0)  
  
satck track  
`  
/u2s/slave/objext/objext/VideoObjectExtractionService.3.1.6.0-cuda10010[0x41e43a]  
/u2s/slave/objext/objext/VideoObjectExtractionService.3.1.6.0-cuda10010[0x41eaad]  
/lib/x86_64-linux-gnu/libc.so.6(+0x354b0)[0x7f00ee0684b0]  
/lib/x86_64-linux-gnu/libc.so.6(gsignal+0x38)[0x7f00ee068428]  
/lib/x86_64-linux-gnu/libc.so.6(abort+0x16a)[0x7f00ee06a02a]  
/lib/x86_64-linux-gnu/libc.so.6(+0x2dbd7)[0x7f00ee060bd7]  
/lib/x86_64-linux-gnu/libc.so.6(+0x2dc82)[0x7f00ee060c82]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost5beast9websocket6streamINS_4asio19basic_stream_socketINS3_2ip3tcpEEELb1EE12read_some_opINS3_14mutable_bufferENS8_7read_opINS0_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSH_St12_PlaceholderILi1EESO_ILi2EEEEEEEclESJ_mb+0x2155)[0x7eff9c5bdd3f]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail7binder2INS_5beast9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpEEELb1EE12read_some_opINS0_14mutable_bufferENSA_7read_opINS3_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSJ_St12_PlaceholderILi1EESQ_ILi2EEEEEEEESL_mEclEv+0x40)[0x7eff9c62a09e]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio19asio_handler_invokeINS0_6detail7binder2INS_5beast9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpEEELb1EE12read_some_opINS0_14mutable_bufferENSB_7read_opINS4_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSK_St12_PlaceholderILi1EESR_ILi2EEEEEEEESM_mEEEEvRT_z+0x68)[0x7eff9c627c44]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost5beast9websocket19asio_handler_invokeIRNS_4asio6detail7binder2INS1_6streamINS3_19basic_stream_socketINS3_2ip3tcpEEELb1EE12read_some_opINS3_14mutable_bufferENSB_7read_opINS0_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSK_St12_PlaceholderILi1EESR_ILi2EEEEEEEESM_mEEEEvOT_PSW_+0x37)[0x7eff9c624a13]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost5beast9websocket19asio_handler_invokeIRNS_4asio6detail7binder2INS1_6streamINS3_19basic_stream_socketINS3_2ip3tcpEEELb1EE12read_some_opINS3_14mutable_bufferENSB_7read_opINS0_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSK_St12_PlaceholderILi1EESR_ILi2EEEEEEEESM_mEEEEvOT_PSX_+0x32)[0x7eff9c620935]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN33boost_asio_handler_invoke_helpers6invokeIN5boost4asio6detail7binder2INS1_5beast9websocket6streamINS2_19basic_stream_socketINS2_2ip3tcpEEELb1EE12read_some_opINS2_14mutable_bufferENSC_7read_opINS5_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS1_6system10error_codeEmEEPSL_St12_PlaceholderILi1EESS_ILi2EEEEEEEESN_mEESY_EEvRT_RT0_+0x2e)[0x7eff9c61af4d]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail19asio_handler_invokeINS1_7binder2INS_5beast9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpEEELb1EE12read_some_opINS0_14mutable_bufferENSB_7read_opINS4_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSK_St12_PlaceholderILi1EESR_ILi2EEEEEEEESM_mEESX_SM_mEEvRT_PNS3_IT0_T1_T2_EE+0x23)[0x7eff9c613c2d]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN33boost_asio_handler_invoke_helpers6invokeIN5boost4asio6detail7binder2INS1_5beast9websocket6streamINS2_19basic_stream_socketINS2_2ip3tcpEEELb1EE12read_some_opINS2_14mutable_bufferENSC_7read_opINS5_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS1_6system10error_codeEmEEPSL_St12_PlaceholderILi1EESS_ILi2EEEEEEEESN_mEESZ_EEvRT_RT0_+0x2e)[0x7eff9c60a60c]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNK5boost4asio10io_context13executor_type8dispatchINS0_6detail7binder2INS_5beast9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpEEELb1EE12read_some_opINS0_14mutable_bufferENSD_7read_opINS6_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSM_St12_PlaceholderILi1EEST_ILi2EEEEEEEESO_mEESaIvEEEvOT_RKT0_+0x94)[0x7eff9c600464]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail12handler_workINS_5beast9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpEEELb1EE12read_some_opINS0_14mutable_bufferENSA_7read_opINS3_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSJ_St12_PlaceholderILi1EESQ_ILi2EEEEEEEENS0_10io_context13executor_typeEE8completeINS1_7binder2ISW_SL_mEEEEvRT_RSW_+0x4d)[0x7eff9c5f4525]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail23reactive_socket_recv_opINS_5beast19buffers_prefix_viewINS3_14buffers_suffixINS0_14mutable_bufferEEEEENS3_9websocket6streamINS0_19basic_stream_socketINS0_2ip3tcpEEELb1EE12read_some_opIS6_NSF_7read_opINS3_17basic_flat_bufferISaIcEEESt5_BindIFSt7_Mem_fnIM9CWSClientFvNS_6system10error_codeEmEEPSN_St12_PlaceholderILi1EESU_ILi2EEEEEEEEE11do_completeEPvPNS1_19scheduler_operationERKSP_m+0x133)[0x7eff9c5ea395]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail19scheduler_operation8completeEPvRKNS_6system10error_codeEm+0x32)[0x7eff9c58a5d2]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail9scheduler10do_run_oneERNS1_27conditionally_enabled_mutex11scoped_lockERNS1_21scheduler_thread_infoERKNS_6system10error_codeE+0x1c2)[0x7eff9c58e1e2]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio6detail9scheduler3runERNS_6system10error_codeE+0xf1)[0x7eff9c58dc51]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN5boost4asio10io_context3runEv+0x3e)[0x7eff9c58f094]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZN9CWSClient13receiveThreadEv+0x91)[0x7eff9c588c6d]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNKSt12_Mem_fn_baseIM9CWSClientFivELb1EEclIIEvEEiPS0_DpOT_+0x65)[0x7eff9c6307dd]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNSt5_BindIFSt7_Mem_fnIM9CWSClientFivEEPS1_EE6__callIiJEJLm0EEEET_OSt5tupleIJDpT0_EESt12_Index_tupleIJXspT1_EEE+0x57)[0x7eff9c63073f]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNSt5_BindIFSt7_Mem_fnIM9CWSClientFivEEPS1_EEclIJEiEET0_DpOT_+0x39)[0x7eff9c630695]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNSt12_Bind_simpleIFSt5_BindIFSt7_Mem_fnIM9CWSClientFivEEPS2_EEvEE9_M_invokeIIEEEiSt12_Index_tupleIIXspT_EEE+0x28)[0x7eff9c63058c]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNSt12_Bind_simpleIFSt5_BindIFSt7_Mem_fnIM9CWSClientFivEEPS2_EEvEEclEv+0x2c)[0x7eff9c62fff0]  
/u2s/slave/objext/objext/DecoderPlugins/DecoderMAS/libDecoderMAS.so(_ZNSt6thread5_ImplISt12_Bind_simpleIFSt5_BindIFSt7_Mem_fnIM9CWSClientFivEEPS4_EEvEEE6_M_runEv+0x1c)[0x7eff9c62f472]  
/usr/lib/x86_64-linux-gnu/libstdc++.so.6(+0xb8c80)[0x7f00eede0c80]  
/lib/x86_64-linux-gnu/libpthread.so.0(+0x76ba)[0x7f00ef0b16ba]  
/lib/x86_64-linux-gnu/libc.so.6(clone+0x6d)[0x7f00ee13a3dd]  
`  
  
  
gcc -v  
`  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 5.4.0-6ubuntu1~16.04.10' --with-bugurl=file:///usr/share/doc/gcc-5/README.Bugs --enable-languages=c,ada,c++,java,go,d,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-5 --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --with-system-zlib --disable-browser-plugin --enable-java-awt=gtk --enable-gtk-cairo --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-5-amd64/jre --enable-java-home --with-jvm-root-dir=/usr/lib/jvm/java-1.5.0-gcj-5-amd64 --with-jvm-jar-dir=/usr/lib/jvm-exports/java-1.5.0-gcj-5-amd64 --with-arch-directory=amd64 --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --enable-objc-gc --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.10)  
`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-22 13:30:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-523906900  

This zlib thing again

---

## Comment 2

> Username: xiongguangjie  
> Created at: 2019-08-22 14:40:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-523935381  

i not use beast::websocket::stream::set_option to use zlib compress  
should i use it ? or do others thing

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-22 14:42:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-523936433  

You can try turning it off and see if the problem goes away. It is on by default for clients, and off by default for servers.

---

## Comment 4

> Username: xiongguangjie  
> Created at: 2019-08-23 02:11:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-524144901  

beast assert on client , i call `websocket::stream::get_option` after handshake  and before handshake  ,  
the `websocket::permessage_deflate::client_enable` and `websocket::permessage_deflate::server_enable` both is false   
server also boost 1.69.0

---

## Comment 5

> Username: djarek  
> Created at: 2019-08-27 07:17:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-525173391  

This doesn't look like a zlib issue (and as @xiongguangjie said, he seems to be using the defaults on the server, i.e. compression disabled). I think that this issue was fixed in Boost.ASIO 1.70: https://github.com/boostorg/asio/pull/182

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-09-26 08:17:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-535394427  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-10-03 09:16:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1685#issuecomment-537861602  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
