cmake_minimum_required(VERSION 3.20..3.26)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


project(
  MPI_FAIL
  VERSION 0.0.0
  LANGUAGES C CXX)

find_package(MPI REQUIRED)
find_package(OpenMP REQUIRED)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_INCLUDE_WHAT_YOU_USE=iwyu)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fstrict-enums -Wall -Wextra -Wpedantic -Werror -g")

if(CUDA)
  message("CUDA Enabled")
  find_package(CUDAToolkit REQUIRED)
  enable_language(CUDA)
  add_compile_definitions(CUDA_AVAIL)
  set(CMAKE_CUDA_ARCHITECTURES "70;70;75;80;86")
  set(CMAKE_CUDA_STANDARD 17)
endif()
if(HIP)
  message("HIP Enabled")
  find_package(HIP REQUIRED)
  find_package(hipBLAS REQUIRED)
  find_package(hipSOLVER REQUIRED)
  enable_language(HIP)
  add_compile_definitions(HIP_AVAIL)
  set(CMAKE_HIP_SOURCE_FILE_EXTENSIONS hip;cu)
  file(GLOB_RECURSE DEVICE_FILES *.cu)
  set_source_files_properties(${DEVICE_FILES} PROPERTIES LANGUAGE HIP)
  set(CMAKE_HIP_ARCHITECTURES "gfx90a")
endif()

add_executable(fail)

target_link_libraries(fail PRIVATE MPI::MPI_CXX OpenMP::OpenMP_CXX)

target_include_directories(fail PRIVATE 
  ${CMAKE_CURRENT_SOURCE_DIR}/src
#  /opt/cray/pe/mpich/8.1.23/ofi/crayclang/10.0/include
  ${CMAKE_CURRENT_SOURCE_DIR}/src/lib)

target_sources(fail PRIVATE a.cu)
