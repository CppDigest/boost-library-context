# #17 - Create tests for premultiply patch [Closed]

> Username: christian-henning  
> Created at: 2017-07-01 19:57:23 UTC  
> Updated at: 2018-04-24 17:33:47 UTC  
> Closed at: 2018-04-04 15:41:26 UTC  
> Url: https://github.com/boostorg/gil/issues/17  



---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-07-02 16:36:58 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312502448  

The PR is https://github.com/boostorg/gil/pull/21...

---

## Comment 2

> Username: christian-henning  
> Created at: 2017-07-03 00:57:06 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312527846  

After adding the patch this simple code still breaks the compliation:  
  
	gray1_image_t img;  
	write_view( "", view( img ), tiff_tag() );

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-07-03 05:14:12 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312551189  

Can you post the build command, as well as the error log, please ?  
Thanks,

---

## Comment 4

> Username: chhenning  
> Created at: 2017-07-03 13:57:35 UTC  
> Updated at: 2018-04-24 17:33:47 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312652599  

```  
1>------ Build started: Project: git_test, Configuration: Debug x64 ------  
1>  main.cpp  
1>d:\boost\boost\gil\color_convert.hpp(235): error C2440: 'return': cannot convert from 'boost::gil::packed_channel_value<1>' to 'boost::gil::packed_dynamic_channel_reference<BitField,1,false>'  
1>          with  
1>          [  
1>              BitField=unsigned char  
1>          ]  
1>  d:\boost\boost\gil\color_convert.hpp(235): note: No constructor could take the source type, or constructor overload resolution was ambiguous  
1>  d:\boost\boost\gil\color_convert.hpp(242): note: see reference to function template instantiation 'boost::gil::packed_dynamic_channel_reference<BitField,1,false> boost::gil::detail::alpha_or_max_impl<Pixel>(const Pixel &,boost::mpl::false_)' being compiled  
1>          with  
1>          [  
1>              BitField=unsigned char,  
1>              Pixel=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>  
1>          ]  
1>  d:\boost\boost\gil\premultiply.hpp(29): note: see reference to function template instantiation 'boost::gil::packed_dynamic_channel_reference<BitField,1,false> boost::gil::alpha_or_max<SrcP>(const Pixel &)' being compiled  
1>          with  
1>          [  
1>              BitField=unsigned char,  
1>              SrcP=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,  
1>              Pixel=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>  
1>          ]  
1>  d:\boost\boost\mpl\for_each.hpp(78): note: see reference to function template instantiation 'void boost::gil::channel_premultiply<SrcP,DstP>::operator ()<T>(Channel) const' being compiled  
1>          with  
1>          [  
1>              SrcP=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>,  
1>              T=arg,  
1>              Channel=arg  
1>          ]  
1>  d:\boost\boost\mpl\for_each.hpp(78): note: see reference to function template instantiation 'void boost::gil::channel_premultiply<SrcP,DstP>::operator ()<T>(Channel) const' being compiled  
1>          with  
1>          [  
1>              SrcP=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>,  
1>              T=arg,  
1>              Channel=arg  
1>          ]  
1>  d:\boost\boost\mpl\for_each.hpp(105): note: see reference to function template instantiation 'void boost::mpl::aux::for_each_impl<false>::execute<first,last,TransformOp,F>(Iterator *,LastIterator *,TransformFunc *,F)' being compiled  
1>          with  
1>          [  
1>              TransformOp=boost::mpl::identity<boost::mpl::na>,  
1>              F=boost::gil::channel_premultiply<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>,  
1>              Iterator=first,  
1>              LastIterator=last,  
1>              TransformFunc=boost::mpl::identity<boost::mpl::na>  
1>          ]  
1>  d:\boost\boost\mpl\for_each.hpp(118): note: see reference to function template instantiation 'void boost::mpl::for_each<Sequence,boost::mpl::identity<boost::mpl::na>,F>(F,Sequence *,TransformOp *)' being compiled  
1>          with  
1>          [  
1>              Sequence=src_colour_channels,  
1>              F=boost::gil::channel_premultiply<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>,  
1>              TransformOp=boost::mpl::identity<boost::mpl::na>  
1>          ]  
1>  d:\boost\boost\gil\premultiply.hpp(64): note: see reference to function template instantiation 'void boost::mpl::for_each<src_colour_channels,boost::gil::channel_premultiply<SrcP,DstP>>(F,Sequence *)' being compiled  
1>          with  
1>          [  
1>              SrcP=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>,  
1>              F=boost::gil::channel_premultiply<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>,  
1>              Sequence=src_colour_channels  
1>          ]  
1>  d:\boost\boost\gil\premultiply.hpp(84): note: see reference to function template instantiation 'void boost::gil::premultiply::operator ()<boost::gil::bit_aligned_pixel_reference<BitField,ChannelBitSizes,Layout,false>,boost::gil::packed_pixel<BitField,boost::mpl::vector1<T>,Layout>>(const SrcP &,DstP &) const' being compiled  
1>          with  
1>          [  
1>              BitField=unsigned char,  
1>              ChannelBitSizes=boost::mpl::vector1_c<unsigned int,1>,  
1>              Layout=boost::gil::gray_layout_t,  
1>              T=const boost::gil::packed_channel_reference<unsigned char,0,1,true>,  
1>              SrcP=boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>  
1>          ]  
1>  d:\boost\boost\gil\premultiply.hpp(82): note: while compiling class template member function 'boost::gil::packed_pixel<BitField,boost::mpl::vector1<T>,Layout> boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<BitField,ChannelBitSizes,Layout,false>,DstP>::operator ()(const boost::gil::bit_aligned_pixel_reference<BitField,ChannelBitSizes,Layout,false>) const'  
1>          with  
1>          [  
1>              BitField=unsigned char,  
1>              T=const boost::gil::packed_channel_reference<unsigned char,0,1,true>,  
1>              Layout=boost::gil::gray_layout_t,  
1>              ChannelBitSizes=boost::mpl::vector1_c<unsigned int,1>,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>  
1>          ]  
1>  d:\boost\boost\gil\pixel_iterator_adaptor.hpp(89): note: see reference to function template instantiation 'boost::gil::packed_pixel<BitField,boost::mpl::vector1<T>,Layout> boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<BitField,ChannelBitSizes,Layout,false>,DstP>::operator ()(const boost::gil::bit_aligned_pixel_reference<BitField,ChannelBitSizes,Layout,false>) const' being compiled  
1>          with  
1>          [  
1>              BitField=unsigned char,  
1>              T=const boost::gil::packed_channel_reference<unsigned char,0,1,true>,  
1>              Layout=boost::gil::gray_layout_t,  
1>              ChannelBitSizes=boost::mpl::vector1_c<unsigned int,1>,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>  
1>          ]  
1>  d:\boost\boost\gil\pixel_iterator_adaptor.hpp(46): note: see reference to class template instantiation 'boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<BitField,ChannelBitSizes,Layout,false>,DstP>' being compiled  
1>          with  
1>          [  
1>              BitField=unsigned char,  
1>              ChannelBitSizes=boost::mpl::vector1_c<unsigned int,1>,  
1>              Layout=boost::gil::gray_layout_t,  
1>              DstP=boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>  
1>          ]  
1>  c:\program files (x86)\microsoft visual studio 14.0\vc\include\xutility(620): note: see reference to class template instantiation 'boost::gil::dereference_iterator_adaptor<Iterator,Deref>' being compiled  
1>          with  
1>          [  
1>              Iterator=boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,  
1>              Deref=boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>  
1>          ]  
1>  d:\boost\boost\gil\locator.hpp(125): note: see reference to class template instantiation 'std::iterator_traits<boost::gil::dereference_iterator_adaptor<Iterator,Deref>>' being compiled  
1>          with  
1>          [  
1>              Iterator=boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,  
1>              Deref=boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>  
1>          ]  
1>  d:\boost\boost\gil\locator.hpp(238): note: see reference to class template instantiation 'boost::gil::pixel_2d_locator_base<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::dereference_iterator_adaptor<Iterator,Deref>>>,boost::gil::dereference_iterator_adaptor<Iterator,Deref>,StepIterator>' being compiled  
1>          with  
1>          [  
1>              Iterator=boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,  
1>              Deref=boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>,  
1>              StepIterator=boost::gil::memory_based_step_iterator<boost::gil::dereference_iterator_adaptor<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>>>  
1>          ]  
1>  d:\boost\boost\gil\image_view.hpp(73): note: see reference to class template instantiation 'boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::dereference_iterator_adaptor<Iterator,Deref>>>' being compiled  
1>          with  
1>          [  
1>              Iterator=boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,  
1>              Deref=boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\formats\tiff\write.hpp(184): note: see reference to class template instantiation 'boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::dereference_iterator_adaptor<Iterator,Deref>>>>' being compiled  
1>          with  
1>          [  
1>              Iterator=boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,  
1>              Deref=boost::gil::premultiply_deref_fn<const boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,false>,boost::gil::packed_pixel<unsigned char,boost::mpl::vector1<const boost::gil::packed_channel_reference<unsigned char,0,1,true>>,boost::gil::gray_layout_t>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\formats\tiff\write.hpp(147): note: see reference to function template instantiation 'void boost::gil::writer<boost::gil::detail::file_stream_device<boost::gil::tiff_tag>,FormatTag,boost::gil::no_log>::write_data<View>(const View &,std::size_t,const boost::mpl::true_ &)' being compiled  
1>          with  
1>          [  
1>              FormatTag=boost::gil::tiff_tag,  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\formats\tiff\write.hpp(144): note: see reference to function template instantiation 'void boost::gil::writer<boost::gil::detail::file_stream_device<boost::gil::tiff_tag>,FormatTag,boost::gil::no_log>::write_data<View>(const View &,std::size_t,const boost::mpl::true_ &)' being compiled  
1>          with  
1>          [  
1>              FormatTag=boost::gil::tiff_tag,  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\formats\tiff\write.hpp(125): note: see reference to function template instantiation 'void boost::gil::writer<boost::gil::detail::file_stream_device<boost::gil::tiff_tag>,FormatTag,boost::gil::no_log>::write_view<View>(const View &)' being compiled  
1>          with  
1>          [  
1>              FormatTag=boost::gil::tiff_tag,  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\formats\tiff\write.hpp(125): note: see reference to function template instantiation 'void boost::gil::writer<boost::gil::detail::file_stream_device<boost::gil::tiff_tag>,FormatTag,boost::gil::no_log>::write_view<View>(const View &)' being compiled  
1>          with  
1>          [  
1>              FormatTag=boost::gil::tiff_tag,  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\detail\write_view.hpp(51): note: see reference to function template instantiation 'void boost::gil::writer<boost::gil::detail::file_stream_device<boost::gil::tiff_tag>,FormatTag,boost::gil::no_log>::apply<View>(const View &)' being compiled  
1>          with  
1>          [  
1>              FormatTag=boost::gil::tiff_tag,  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\detail\write_view.hpp(51): note: see reference to function template instantiation 'void boost::gil::writer<boost::gil::detail::file_stream_device<boost::gil::tiff_tag>,FormatTag,boost::gil::no_log>::apply<View>(const View &)' being compiled  
1>          with  
1>          [  
1>              FormatTag=boost::gil::tiff_tag,  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>  
1>          ]  
1>  d:\boost\boost\gil\extension\io\detail\write_view.hpp(113): note: see reference to function template instantiation 'void boost::gil::write_view<writer_t,View>(Writer &,const View &,void *)' being compiled  
1>          with  
1>          [  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>,  
1>              Writer=writer_t  
1>          ]  
1>  c:\users\chhenning\boost\git_test\main.cpp(29): note: see reference to function template instantiation 'void boost::gil::write_view<char[1],boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<XIterator>>>,boost::gil::tiff_tag>(const String (&),const View &,const FormatTag &,void *)' being compiled  
1>          with  
1>          [  
1>              XIterator=boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>,  
1>              String=char [1],  
1>              View=boost::gil::image_view<boost::gil::memory_based_2d_locator<boost::gil::memory_based_step_iterator<boost::gil::bit_aligned_pixel_iterator<boost::gil::bit_aligned_pixel_reference<unsigned char,boost::mpl::vector1_c<unsigned int,1>,boost::gil::gray_layout_t,true>>>>>,  
1>              FormatTag=boost::gil::tiff_tag  
1>          ]  
1>  d:\boost\boost\bind\placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
1>  d:\boost\boost\bind\placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 5

> Username: chhenning  
> Created at: 2017-07-03 13:58:41 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312652845  

Maybe better format:  
  
https://pastebin.com/raw/mZsvMQDw

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-07-03 14:02:32 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312653739  

` Build started: Project: git_test, Configuration: Debug x64 ` ...that doesn't look like `b2` output. What command do you use ? Can you please use the `./b2 ...` command-line ? That is what is used on appveyor, and that's what generates the (runtime) failure (which I can reproduce in my Windows VM), which I'd like to understand and fix.  
Thanks,

---

## Comment 7

> Username: chhenning  
> Created at: 2017-07-03 14:07:05 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312654869  

When you try to compile tiff_tiled_minisblack_write_test_21-31_32-64.cpp with the macro  
  
BOOST_GIL_IO_USE_TIFF_GRAPHICSMAGICK_TEST_SUITE_IMAGES

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2017-07-03 14:24:47 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-312659226  

Can you please use `b2` to build ? I don't doubt that in this particular issue I can reproduce this myself by adding `-DBOOST_GIL_IO_USE_TIFF_GRAPHICSMAGICK_TEST_SUITE_IMAGES` to the command line, but for all the other work we are doing, it's much simpler to make reproducible test cases if we use a command-line tool, in particular if that's what the CI environments use, too.

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2018-04-04 15:41:26 UTC  
> Url: https://github.com/boostorg/gil/issues/17#issuecomment-378646428  

This can be closed as obsolete.
