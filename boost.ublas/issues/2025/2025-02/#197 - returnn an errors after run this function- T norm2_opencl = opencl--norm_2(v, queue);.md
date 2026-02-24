# #197 - returnn an errors after run this function: T norm2_opencl = opencl::norm_2(v, queue); [Open]

> Username: MichaelSipayung  
> Created at: 2025-02-13 16:02:45 UTC  
> Updated at: 2025-02-13 16:02:45 UTC  
> Url: https://github.com/boostorg/ublas/issues/197  

[asus@localhost build]$ ./boost_ublas_sample   
AN INTERNAL KERNEL BUILD ERROR OCCURRED!  
device name = 12th Gen Intel(R) Core(TM) i5-1235U  
error = -11  
memory pattern = Register accumulation based Nrm2, computing kernel generator  
Subproblem dimensions: dims[0].itemY = 32, dims[0].itemX = 32, dims[0].y = 32, dims[0].x = 32, dims[0].bwidth = 64; ; dims[1].itemY = 4, dims[1].itemX = 4, dims[1].y = 4, dims[1].x = 4, dims[1].bwidth = 8; ;   
Parallelism granularity: pgran->wgDim = 1, pgran->wgSize[0] = 64, pgran->wgSize[1] = 1, pgran->wfSize = 64  
Kernel extra flags: 268446368  
Source:  
  
#ifdef DOUBLE_PRECISION  
    #ifdef cl_khr_fp64  
    #pragma OPENCL EXTENSION cl_khr_fp64 : enable  
    #else  
    #pragma OPENCL EXTENSION cl_amd_fp64 : enable  
    #endif  
    #define MAX 0x1.fffffffffffffp1023      // Max in case of d/z (values from khronos site)  
#else  
    #define MAX 0x1.fffffep127f             // Max in case of s/c  
#endif  
  
#define PZERO (float)0.0  
#define ZERO (float)0.0  
#define VZERO (float4)0.0  
  
//  
// Same scratch buffer will be used both scale and ssq.  
// So a scratch buffer of size 2*N is needed.  
// scale will be stored in scratch-buffer from [0] to [get_num_groups(0) - 1]  
// ssq will be stored from [get_num_groups(0)] to [2*get_num_groups(0) - 1]  
//  
  
__kernel void Snrm2_ssq_kernel( __global float *_X, __global float *scratchBuff,  
                                        uint N, uint offx, int incx )  
{  
    __global float *X = _X + offx;  
    uint numWGs = get_num_groups(0);  
  
    #ifdef RETURN_ON_INVALID  
        // Incase of incx<1, NRM2 will be zero  
        if( get_global_id(0) == 0 ) {  
            scratchBuff[0] = PZERO;  
            scratchBuff[numWGs] = PZERO;  
        }  
        return;  
    #endif  
  
    // First we find the max element in the whole work-group  
    // i.e calculating scale  
    float maxFound = (float) -MAX;  
  
    int gOffset;  
    for( gOffset=(get_global_id(0) * 4); (gOffset + 4 - 1)<N; gOffset+=( get_global_size(0) * 4 ) )  
    {  
        float4 vReg1;  
  
        #ifdef INCX_NONUNITY  
             vReg1 = (float4)(  (X + (gOffset*incx))[0 + ( incx * 0)],  (X + (gOffset*incx))[0 + ( incx * 1)],  (X + (gOffset*incx))[0 + ( incx * 2)],  (X + (gOffset*incx))[0 + ( incx * 3)]);  
        #else  
            vReg1 = *((__global float4*)( (X + gOffset) ));  
        #endif  
  
        vReg1 = fabs( vReg1 );  
        float regMax = fmax(  vReg1 .S0, fmax(  vReg1 .S1, fmax(  vReg1 .S2,   vReg1 .S3 )));  
  
        maxFound = fmax( maxFound, regMax );  
    }  
  
    for( ; gOffset<N; gOffset++ )  
    {  
        float sReg1;  
  
        sReg1 = X[gOffset * incx];  
        sReg1 = fabs( sReg1 );  
        maxFound = fmax( maxFound, sReg1 );  
    }  
  
    __local float ASa1X [ 64 ];  
        uint vPOXr = get_local_id(0);  
         ASa1X [ vPOXr ] =  maxFound ;  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( vPOXr < 32 ) {  
                 ASa1X [ vPOXr ] = fmax( ASa1X [ vPOXr ] , ASa1X [ vPOXr + 32 ] );  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( vPOXr < 16 ) {  
                 ASa1X [ vPOXr ] = fmax( ASa1X [ vPOXr ] , ASa1X [ vPOXr + 16 ] );  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( vPOXr < 8 ) {  
                 ASa1X [ vPOXr ] = fmax( ASa1X [ vPOXr ] , ASa1X [ vPOXr + 8 ] );  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( vPOXr < 4 ) {  
                 ASa1X [ vPOXr ] = fmax( ASa1X [ vPOXr ] , ASa1X [ vPOXr + 4 ] );  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( vPOXr < 2 ) {  
                 ASa1X [ vPOXr ] = fmax( ASa1X [ vPOXr ] , ASa1X [ vPOXr + 2 ] );  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( vPOXr == 0 ) {  
         maxFound  = fmax( ASa1X [0] , ASa1X [1] );  
        }  
  
    __local float _scale;  
  
    if( (get_local_id(0)) == 0 ) {  
        #ifdef COMPLEX  
            _scale = fmax( maxFound.even, maxFound.odd );  
        #else  
            _scale = maxFound;  
        #endif  
    }  
    barrier(CLK_LOCAL_MEM_FENCE);  
  
    // At this point we have scale.  
    // Now we calculate ssq by loading the array again and dividing the  
    // elements by scale and squaring it.  
  
    float ssq = ZERO;  
    float scaleOfWG = _scale;  
  
    // If scaleOfWG was zero, that means the whole array encountered before was filled with zeroes  
    // Note: _scale is a local variable, either all enter or none  
    if(isnotequal(scaleOfWG, PZERO))  
    {  
        for( gOffset=(get_global_id(0) * 4); (gOffset + 4 - 1)<N; gOffset+=( get_global_size(0) * 4 ) )  
        {  
            float4 vReg1;  
  
            #ifdef INCX_NONUNITY  
                 vReg1 = (float4)(  (X + (gOffset*incx))[0 + ( incx * 0)],  (X + (gOffset*incx))[0 + ( incx * 1)],  (X + (gOffset*incx))[0 + ( incx * 2)],  (X + (gOffset*incx))[0 + ( incx * 3)]);  
            #else  
                vReg1 = *((__global float4*)( (X + gOffset) ));  
            #endif  
  
            vReg1 = fabs( vReg1 );  
            float4 tempSsq = (vReg1 / scaleOfWG) * (vReg1 / scaleOfWG);  
  
            ssq +=  tempSsq .S0 +  tempSsq .S1 +  tempSsq .S2 +  tempSsq .S3;  
;  
        }  
  
        for( ; gOffset<N; gOffset++ )  
        {  
            float sReg1;  
  
            sReg1 = X[gOffset * incx];  
            sReg1 = fabs( sReg1 );  
  
            ssq += (sReg1 / scaleOfWG) * (sReg1 / scaleOfWG);  
        }  
  
        __local float tW9eD [ 64 ];  
        uint Aow5C = get_local_id(0);  
         tW9eD [ Aow5C ] =  ssq ;  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( Aow5C < 32 ) {  
                 tW9eD [ Aow5C ] = tW9eD [ Aow5C ] + tW9eD [ Aow5C + 32 ];  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( Aow5C < 16 ) {  
                 tW9eD [ Aow5C ] = tW9eD [ Aow5C ] + tW9eD [ Aow5C + 16 ];  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( Aow5C < 8 ) {  
                 tW9eD [ Aow5C ] = tW9eD [ Aow5C ] + tW9eD [ Aow5C + 8 ];  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( Aow5C < 4 ) {  
                 tW9eD [ Aow5C ] = tW9eD [ Aow5C ] + tW9eD [ Aow5C + 4 ];  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( Aow5C < 2 ) {  
                 tW9eD [ Aow5C ] = tW9eD [ Aow5C ] + tW9eD [ Aow5C + 2 ];  
        }  
        barrier(CLK_LOCAL_MEM_FENCE);  
  
        if( Aow5C == 0 ) {  
         ssq  = tW9eD [0] + tW9eD [1];  
        }  
    }  
  
    if( (get_local_id(0)) == 0 ) {  
        scratchBuff[ get_group_id(0) ] = scaleOfWG;  
  
        #ifdef COMPLEX  
            scratchBuff[ numWGs + get_group_id(0) ] = ssq.even + ssq.odd;  
        #else  
            scratchBuff[ numWGs + get_group_id(0) ] = ssq;  
        #endif  
    }  
}  
  
  
  
--------------------------------------------------------  
  
Build log:  
  
Compilation started  
1:151:23: error: variables in the local address space can only be declared in the outermost scope of a kernel function  
  151 |         __local float tW9eD [ 64 ];  
      |                       ^  
Compilation failed  
  
========================================================  
  
Segmentation fault (core dumped)  
[asus@localhost build]$
