CMAKE_MINIMUM_REQUIRED(VERSION 3.2)
project(grimCUDAMarchingCubes)


message(";; source directory: ${PROJECT_SOURCE_DIR}")
message(";; binary directory: ${PROJECT_BINARY_DIR}")



# Get a list of all of the source files in the directory:
file(GLOB SOURCE
    "*.h" 
    "*.cpp"
)

ADD_LIBRARY(grimCudaMCPP_lib ${SOURCE})

find_package(CUDA REQUIRED)  

find_package(OpenGL REQUIRED)
if(OPENGL_FOUND)
	set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenGL_CXX_FLAGS}")
endif()


find_package(OpenMP REQUIRED)
if(OPENMP_FOUND)
	set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

INCLUDE_DIRECTORIES(${CUDA_PATH}/include)
INCLUDE_DIRECTORIES(${CUDA_PATH}/samples/common/inc)

# nvcc flags                                                                                                                                                                                               

set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} --std=c++11 --keep --generate-line-info 
--source-in-ptx -Xcompiler -Wall -Xcompiler -D__CORRECT_ISO_CPP11_MATH_H_PROTO )




LIST(APPEND CUDA_NVCC_FLAGS --compiler-options -fno-strict-aliasing -lineinfo -use_fast_math -Xptxas -dlcm=cg -Wno-deprecated-gpu-targets)



cuda_compile(grimCudaMC_O marchingCubes_kernel.cu OPTIONS -G)

cuda_add_library(grimCudaMC_lib ${grimCudaMC_O})   
 
target_link_libraries(grimCudaMCPP_lib grimCudaMC_lib glut ${OPENGL_LIBRARIES})
#target_link_libraries(grimCudaMC_lib  ${OPENGL_LIBRARIES} grimCudaMCPP_lib)

 



