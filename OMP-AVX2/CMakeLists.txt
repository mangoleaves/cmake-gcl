cmake_minimum_required(VERSION 3.9)
# Add .cmake files in directory 'cmake'
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

project(_ CXX)

# C++ standard 17.
# in C++17 the standard allocators have been updated to respect type's alignment.
set(CMAKE_CXX_STANDARD 17)

# find OpenMesh, Eigen and OpenMP
find_package(Eigen3 REQUIRED)
if(NOT EIGEN3_FOUND)
    message(FATAL_ERROR "Eigen3 Not Found!")
endif(NOT EIGEN3_FOUND)

find_package(OpenMesh REQUIRED)
if(NOT OPENMESH_FOUND)
    message(FATAL_ERROR "OpenMesh Not Found!")
endif(NOT OPENMESH_FOUND)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC" OR
    "${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    find_package(OpenMP REQUIRED)
    if(NOT OPENMP_FOUND)
        message(FATAL_ERROR "OpenMP Not Found!")
    endif()
endif()

# set compiler's options to enable AVX2
if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
    message(STATUS "Use MSVC.")
    add_compile_options(/arch:AVX2)
elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    message(STATUS "Use GNU.")
    include(CheckCXXCompilerFlag)
    check_cxx_compiler_flag("-march=native" COMPILER_SUPPORTS_MARCH_NATIVE)
    if(COMPILER_SUPPORTS_MARCH_NATIVE)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native -mtune=native")
    else()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -mavx2")
    endif()
elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Intel")
    message(STATUS "Use Intel.")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Ofast -march=core-avx2 -fiopenmp -fsycl")
else()
    message(FATAL_ERROR "Unconfigured compiler ${CMAKE_CXX_COMPILER_ID}.")
endif()

# set header files and source files of project
set(_HDRS
    
)

set(_SRCS
)

# build library
add_library(_ STATIC
    ${DM_HDRS}
    ${DM_SRCS}
)
target_include_directories(DM PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${EIGEN3_INCLUDE_DIR}
    ${OPENMESH_INCLUDE_DIR})
if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC" OR
    "${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    target_link_libraries(DM PUBLIC ${OPENMESH_LIBRARIES} OpenMP::OpenMP_CXX)
else()
    target_link_libraries(DM PUBLIC ${OPENMESH_LIBRARIES})
endif()
target_compile_definitions(DM PUBLIC _USE_MATH_DEFINES USE_AVX)

# build an excutable program for testing
add_executable(exe src/main.cpp)
target_link_libraries(exe PUBLIC _ ${OPENMESH_LIBRARIES})
