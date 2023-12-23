cmake_minimum_required(VERSION 3.16)

project(payment_gateway_library VERSION 0.0.1 LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 20)
 
message(STATUS "START COMPILE PACKAGE_TEMPLATE")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")

set(payment_gateway_shared_dir "lib")
set(payment_gateway_exe_dir "bin")
set(prefix_shared_library "lib")
 

if (ANDROID)
 set(package_shared_extension "_${ANDROID_ARCH_NAME}.so")
 set(package_exe_extension "")
 set(package_name "payment_gateway_${ANDROID_ARCH_NAME}")
elseif (WIN32)
 set(package_shared_extension ".dll")
 set(package_exe_extension ".exe") 
 set(package_name "payment_gateway")
elseif (APPLE)
 set(package_shared_extension ".dylib")
 set(package_exe_extension "") 
 set(package_name "payment_gateway")
else()
 set(package_shared_extension ".so")
 set(package_exe_extension "") 
 set(package_name "payment_gateway")
endif()


add_library(payment_gateway_shared SHARED payment_gateway.cpp)
add_executable(payment_gateway_exe payment_gateway.cpp)

set_target_properties(payment_gateway_shared PROPERTIES 
  LIBRARY_OUTPUT_DIRECTORY "${payment_gateway_shared_dir}"
  PREFIX "${prefix_shared_library}"
  OUTPUT_NAME "${package_name}"
)

set_target_properties(payment_gateway_exe PROPERTIES 
  RUNTIME_OUTPUT_DIRECTORY "${payment_gateway_exe_dir}"
  OUTPUT_NAME "${package_name}"
)

if (WIN32)
 set_target_properties(payment_gateway_shared PROPERTIES
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)
endif (WIN32)
 
add_custom_command(
  TARGET payment_gateway_shared POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${payment_gateway_shared_dir}/${prefix_shared_library}payment_gateway${package_shared_extension}"
)

add_custom_command(
  TARGET payment_gateway_exe POST_BUILD 
  COMMAND ${CMAKE_STRIP} --strip-debug --strip-unneeded "${payment_gateway_exe_dir}/payment_gateway${package_exe_extension}" 
)

 