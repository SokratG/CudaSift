cmake_minimum_required(VERSION 3.22)

project(cudaSift)
set(cudaSift_VERSION_MAJOR 2)
set(cudaSift_VERSION_MINOR 0)
set(cudaSift_VERSION_PATCH 0)

set(CPACK_PACKAGE_VERSION_MAJOR "${cudaSift_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${cudaSift_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${cudaSift_VERSION_PATCH}")
set(CPACK_GENERATOR "ZIP")
include(CPack)

find_package(OpenCV REQUIRED)
find_package(CUDA)
if (NOT CUDA_FOUND)
  message(STATUS "CUDA not found. Project will not be built.")
endif(NOT CUDA_FOUND)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
set(CMAKE_NVCC_FLAGS
    ${CMAKE_NVCC_FLAGS};
    -lineinfo
    -ccbin
    # -O3
    -D_FORCE_INLINES
)

set(cuda_sources
  cudaImage.cu 
  cudaImage.h 
  cudaSiftH.cu 
  cudaSiftH.h 
  matching.cu 
  cudaSiftD.h 
  cudaSift.h 
  cudautils.h
)

set(sources
  geomFuncs.cpp 
  mainSift.cpp
)

include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}
)

#SET(CUDA_SEPARABLE_COMPILATION ON)

add_library(cudaSift SHARED ${cuda_sources} ${sources})

set_target_properties(cudaSift PROPERTIES
  COMPILE_FLAGS "${EXTRA_CXX_FLAGS}"
  OUTPUT_NAME "cudaSift"
  CUDA_STANDARD 14
  CUDA_SEPARABLE_COMPILATION ON
)

install(TARGETS cudaSift
        LIBRARY DESTINATION ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}
        PUBLIC_HEADER DESTINATION ${CMAKE_INCLUDE_OUTPUT_DIRECTORY})

target_link_libraries(cudaSift ${CUDA_cudadevrt_LIBRARY} ${OpenCV_LIBS})
