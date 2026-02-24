# #179 Include missing headers across boost/gil/extension/io [Merged]

> Username: mloskot  
> Created at: 2018-12-09 21:46:53 UTC  
> Updated at: 2018-12-17 00:08:29 UTC  
> Merged at: 2018-12-09 22:40:38 UTC  
> Closed at: 2018-12-09 22:40:38 UTC  
> Url: https://github.com/boostorg/gil/pull/179  

Missing headers revealed by compilation of self-contained header tests.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
All self-container header tests compile on Linux with GCC 5.5 (configured with CMake):  
  
```  
$ mkdir _build.gcc5  
$ cd _build.gcc5/  
$ cmake -DBoost_ARCHITECTURE=-x64 ..  
$ make test_compile_headers  
[  1%] Built target test_header_io-error  
[  3%] Built target test_header_virtual_locator  
[  4%] Built target test_header_typedefs  
[  4%] Built target test_header_step_iterator  
[  6%] Built target test_header_rgb  
[  6%] Built target test_header_promote_integral  
[  6%] Built target test_header_premultiply  
[  7%] Built target test_header_position_iterator  
[  7%] Built target test_header_pixel_iterator  
[  9%] Built target test_header_packed_pixel  
[ 11%] Built target test_header_iterator_from_2d  
[ 11%] Built target test_header_io-write_view  
[ 11%] Built target test_header_io-typedefs  
[ 11%] Built target test_header_io-read_and_convert_view  
[ 11%] Built target test_header_io-path_spec  
[ 11%] Built target test_header_io-make_writer  
[ 11%] Built target test_header_version  
[ 11%] Built target test_header_io-make_dynamic_image_reader  
[ 12%] Built target test_header_io-make_backend  
[ 12%] Built target test_header_io-make_reader  
[ 12%] Built target test_header_io-io  
[ 12%] Built target test_header_io-get_writer  
[ 14%] Built target test_header_io-get_write_device  
[ 14%] Built target test_header_io-make_dynamic_image_writer  
[ 14%] Built target test_header_io-get_reader  
[ 14%] Built target test_header_io-get_read_device  
[ 14%] Built target test_header_extension-io-png-read  
[ 14%] Built target test_header_extension-io-png-detail-reader_backend  
[ 14%] Built target test_header_extension-io-png-detail-read  
[ 15%] Built target test_header_extension-io-jpeg-write  
[ 15%] Built target test_header_extension-io-targa-detail-is_allowed  
[ 15%] Built target test_header_rgba  
[ 15%] Built target test_header_extension-io-jpeg-read  
[ 17%] Built target test_header_extension-io-bmp-write  
[ 19%] Built target test_header_extension-io-raw-detail-read  
[ 20%] Built target test_header_extension-io-jpeg-detail-scanline_read  
[ 20%] Built target test_header_extension-io-jpeg-detail-base  
[ 20%] Built target test_header_extension-io-pnm-tags  
[ 20%] Built target test_header_bit_aligned_pixel_reference  
[ 20%] Built target test_header_extension-io-targa-detail-scanline_read  
[ 20%] Built target test_header_extension-io-bmp-tags  
[ 20%] Built target test_header_extension-io-raw-read  
[ 20%] Built target test_header_concepts-color_base  
[ 20%] Built target test_header_extension-toolbox-image_types-indexed_image  
[ 20%] Built target test_header_metafunctions  
[ 20%] Built target test_header_extension-io-pnm  
[ 20%] Built target test_header_extension-io-targa  
[ 20%] Built target test_header_extension-toolbox-metafunctions-get_pixel_type  
[ 20%] Built target test_header_io-scanline_read_iterator  
[ 20%] Built target test_header_extension-io-png-detail-write  
[ 22%] Built target test_header_io-row_buffer_helper  
[ 22%] Built target test_header_extension-io-tiff-detail-write  
[ 22%] Built target test_header_extension-io-bmp-detail-read  
[ 23%] Built target test_header_extension-io-jpeg-detail-writer_backend  
[ 25%] Built target test_header_extension-io-bmp-detail-is_allowed  
[ 25%] Built target test_header_extension-io-bmp-old  
[ 25%] Built target test_header_extension-numeric-pixel_numeric_operations  
[ 25%] Built target test_header_concepts-concept_check  
[ 26%] Built target test_header_concepts  
[ 26%] Built target test_header_extension-io-jpeg-old  
[ 28%] Built target test_header_extension-io-png-old  
[ 28%] Built target test_header_extension-io-targa-tags  
[ 30%] Built target test_header_concepts-pixel_dereference  
[ 30%] Built target test_header_extension-io-raw-detail-supported_types  
[ 30%] Built target test_header_extension-io-jpeg-detail-read  
[ 30%] Built target test_header_extension-io-png-detail-writer_backend  
[ 30%] Built target test_header_io-read_image  
[ 30%] Built target test_header_extension-io-png-tags  
[ 31%] Built target test_header_concepts-color  
[ 31%] Built target test_header_color_base_algorithm  
[ 33%] Built target test_header_extension-io-jpeg-detail-is_allowed  
[ 33%] Built target test_header_extension-io-tiff-read  
[ 33%] Built target test_header_extension-dynamic_image-dynamic_image_all  
[ 33%] Built target test_header_extension-io-jpeg-tags  
[ 33%] Built target test_header_color_convert  
[ 34%] Built target test_header_extension-io-png-write  
[ 36%] Built target test_header_extension-io-tiff-detail-scanline_read  
[ 38%] Built target test_header_extension-io-targa-detail-read  
[ 39%] Built target test_header_concepts-detail-utility  
[ 39%] Built target test_header_concepts-basic  
[ 39%] Built target test_header_concepts-pixel_iterator  
[ 41%] Built target test_header_io-read_and_convert_image  
[ 41%] Built target test_header_extension-io-raw-detail-device  
[ 41%] Built target test_header_point  
[ 42%] Built target test_header_deprecated  
[ 44%] Built target test_header_extension-io-png-detail-scanline_read  
[ 44%] Built target test_header_concepts-channel  
[ 46%] Built target test_header_extension-io-tiff  
[ 47%] Built target test_header_extension-io-bmp-detail-scanline_read  
[ 47%] Built target test_header_concepts-image  
[ 47%] Built target test_header_device_n  
[ 47%] Built target test_header_cmyk  
[ 47%] Built target test_header_extension-io-png-detail-supported_types  
[ 49%] Built target test_header_io-read_image_info  
[ 49%] Built target test_header_extension-io-bmp  
[ 49%] Built target test_header_extension-toolbox-color_converters-rgb_to_luminance  
[ 49%] Built target test_header_concepts-pixel_based  
[ 49%] Built target test_header_extension-io-bmp-detail-supported_types  
[ 49%] Built target test_header_extension-io-pnm-detail-writer_backend  
[ 49%] Built target test_header_algorithm  
[ 50%] Built target test_header_channel  
[ 50%] Built target test_header_extension-io-bmp-detail-reader_backend  
[ 50%] Built target test_header_bit_aligned_pixel_iterator  
[ 50%] Built target test_header_channel_algorithm  
[ 52%] Built target test_header_extension-io-targa-read  
[ 52%] Built target test_header_extension-toolbox-color_spaces  
[ 52%] Built target test_header_extension-io-bmp-detail-write  
[ 53%] Built target test_header_pixel_iterator_adaptor  
[ 53%] Built target test_header_concepts-detail-type_traits  
[ 55%] Built target test_header_extension-numeric-resample  
[ 55%] Built target test_header_planar_pixel_iterator  
[ 55%] Built target test_header_extension-dynamic_image-reduce  
[ 55%] Built target test_header_concepts-fwd  
[ 55%] Built target test_header_extension-dynamic_image-any_image_view  
[ 55%] Built target test_header_planar_pixel_reference  
[ 57%] Built target test_header_extension-dynamic_image-image_view_factory  
[ 58%] Built target test_header_extension-toolbox-image_types-subchroma_image  
[ 58%] Built target test_header_concepts-pixel  
[ 58%] Built target test_header_io-read_view  
[ 60%] Built target test_header_extension-io-tiff-write  
[ 61%] Built target test_header_extension-io-bmp-detail-writer_backend  
[ 61%] Built target test_header_utilities  
[ 63%] Built target test_header_extension-dynamic_image-apply_operation_base  
[ 63%] Built target test_header_image  
[ 65%] Built target test_header_extension-dynamic_image-any_image  
[ 65%] Built target test_header_locator  
[ 65%] Built target test_header_extension-io-pnm-detail-is_allowed  
[ 66%] Built target test_header_extension-io-pnm-detail-read  
[ 66%] Built target test_header_concepts-pixel_locator  
[ 66%] Built target test_header_extension-dynamic_image-apply_operation  
[ 66%] Built target test_header_extension-io-pnm-detail-scanline_read  
[ 66%] Built target test_header_extension-io-pnm-detail-write  
[ 66%] Built target test_header_extension-toolbox-color_spaces-ycbcr  
[ 68%] Built target test_header_extension-io-pnm-read  
[ 68%] Built target test_header_extension-io-targa-old  
[ 68%] Built target test_header_extension-io-jpeg  
[ 68%] Built target test_header_extension-io-pnm-old  
[ 69%] Built target test_header_extension-io-raw  
[ 71%] Built target test_header_color_base  
[ 71%] Built target test_header_extension-io-raw-detail-is_allowed  
[ 71%] Built target test_header_extension-io-raw-detail-reader_backend  
[ 71%] Built target test_header_extension-io-jpeg-detail-write  
[ 73%] Built target test_header_extension-io-raw-tags  
[ 73%] Built target test_header_extension-io-targa-detail-reader_backend  
[ 74%] Built target test_header_concepts-image_view  
[ 76%] Built target test_header_extension-io-targa-detail-supported_types  
[ 76%] Built target test_header_extension-io-targa-detail-write  
[ 76%] Built target test_header_extension-io-targa-detail-writer_backend  
[ 76%] Built target test_header_extension-io-pnm-write  
[ 76%] Built target test_header_extension-io-targa-write  
[ 76%] Built target test_header_extension-io-tiff-detail-device  
[ 76%] Built target test_header_extension-io-png  
[ 77%] Built target test_header_extension-io-tiff-detail-log  
[ 77%] Built target test_header_extension-io-png-detail-base  
[ 77%] Built target test_header_extension-io-tiff-detail-read  
[ 77%] Built target test_header_io-reader_base  
[ 77%] Built target test_header_extension-io-tiff-detail-writer_backend  
[ 79%] Built target test_header_extension-toolbox-toolbox  
[ 79%] Built target test_header_extension-io-tiff-detail-supported_types  
[ 80%] Built target test_header_extension-toolbox-metafunctions-is_homogeneous  
[ 82%] Built target test_header_extension-io-tiff-old  
[ 82%] Built target test_header_extension-io-bmp-read  
[ 82%] Built target test_header_extension-io-tiff-detail-is_allowed  
[ 82%] Built target test_header_extension-io-tiff-tags  
[ 82%] Built target test_header_pixel  
[ 82%] Built target test_header_concepts-point  
[ 82%] Built target test_header_extension-io-tiff-detail-reader_backend  
[ 82%] Built target test_header_extension-numeric-affine  
[ 84%] Built target test_header_extension-numeric-channel_numeric_operations  
[ 84%] Built target test_header_extension-io-jpeg-detail-supported_types  
[ 84%] Built target test_header_extension-numeric-convolve  
[ 84%] Built target test_header_extension-numeric-kernel  
[ 85%] Built target test_header_io-make_scanline_reader  
[ 87%] Built target test_header_extension-io-pnm-detail-supported_types  
[ 87%] Built target test_header_extension-toolbox-color_converters  
[ 88%] Built target test_header_extension-toolbox-color_converters-gray_to_rgba  
[ 90%] Built target test_header_extension-toolbox-color_spaces-cmyka  
[ 90%] Built target test_header_extension-io-jpeg-detail-reader_backend  
[ 90%] Built target test_header_extension-toolbox-color_spaces-hsl  
[ 90%] Built target test_header_extension-toolbox-metafunctions  
[ 92%] Built target test_header_image_view  
[ 92%] Built target test_header_extension-toolbox-color_spaces-gray_alpha  
[ 93%] Built target test_header_extension-toolbox-color_spaces-hsv  
[ 93%] Built target test_header_extension-toolbox-color_spaces-lab  
[ 93%] Built target test_header_extension-toolbox-metafunctions-is_bit_aligned  
[ 93%] Built target test_header_extension-toolbox-color_spaces-xyz  
[ 93%] Built target test_header_extension-dynamic_image-dynamic_at_c  
[ 93%] Built target test_header_image_view_factory  
[ 93%] Built target test_header_extension-toolbox-image_types  
[ 95%] Built target test_header_extension-toolbox-metafunctions-channel_view  
[ 95%] Built target test_header_extension-numeric-algorithm  
[ 95%] Built target test_header_extension-toolbox-metafunctions-channel_type  
[ 95%] Built target test_header_extension-numeric-sampler  
[ 95%] Built target test_header_io-conversion_policies  
[ 95%] Built target test_header_extension-toolbox-metafunctions-get_num_bits  
[ 96%] Built target test_header_extension-toolbox-dynamic_images  
[ 96%] Built target test_header_io-device  
[ 96%] Built target test_header_extension-toolbox-metafunctions-is_similar  
[ 96%] Built target test_header_extension-toolbox-metafunctions-pixel_bit_size  
[ 96%] Built target test_header_extension-dynamic_image-variant  
[ 96%] Built target test_header_gray  
[ 96%] Built target test_header_io-base  
[ 98%] Built target test_header_extension-io-png-detail-is_allowed  
[100%] Built target test_header_io-bit_operations  
[100%] Built target test_header_io-dynamic_io_new  
[100%] Built target test_header_extension-dynamic_image-algorithm  
[100%] Built target test_header_extension-io-pnm-detail-reader_backend  
[100%] Built target test_compile_headers  
```

---
