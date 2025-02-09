cmake_minimum_required(VERSION 2.8.12)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
project(HeurLpBased)

######## Cplex Dependency
find_package(CPLEX REQUIRED)
find_package(CPLEX_CONCERT REQUIRED)
find_package(CPLEX_ILOCPLEX REQUIRED)
find_package(CPLEX_CP REQUIRED)

if(CPLEX_FOUND)
    include_directories(${CPLEX_INCLUDE_DIR})
    link_directories(${CPLEX_LIBRARY})
endif()

if(CPLEX_CONCERT_FOUND)
    include_directories(${CPLEX_CONCERT_INCLUDE_DIR})
    link_directories(${CPLEX_CONCERT_LIBRARY})
endif()

if(CPLEX_ILOCPLEX_FOUND)
    include_directories(${CPLEX_ILOCPLEX_INCLUDE_DIR})
    link_directories(${CPLEX_ILOCPLEX_LIBRARY})
endif()


######## Complier message
set(CMAKE_BUILD_TYPE Release)
message(STATUS "System: ${CMAKE_SYSTEM}")
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "Compiler version: ${CMAKE_CXX_COMPILER_VERSION}")

######## Set output directories.

######## Set C++20 standard
include(CheckCXXCompilerFlag)
check_cxx_compiler_flag("-std=c++20" COMPILER_SUPPORTS_CXX20)
if (COMPILER_SUPPORTS_CXX11)
    if (CMAKE_COMPILER_IS_GNUCXX)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++20")
    else ()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++20")
    endif ()
else ()
    message(STATUS "Fatal Error: Your compiler doesn't support c++20, please install it!")
endif ()

######## find all the source files.
file(GLOB HEADER_FILES include/*.h)
file(GLOB SOURCE_FILES src/*.cpp)

######## Set executable file name, and add the source files for it.
add_executable(HeurLpBased ${HEADER_FILES} ${SOURCE_FILES} src/PickSet.h)
######## Add Dependency Library
target_link_libraries(HeurLpBased cplex-library cplex-concert ilocplex)

####### Create directory
set(CREATE_DIR_LOGS logs)
set(CREATE_DIR_MODELS models)
set(CREATE_DIR_INSTANCES instances)

message(STATUS "Install directory: ${CREATE_DIR_LOGS}")
file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/build/bin/${CREATE_DIR_LOGS})

message(STATUS "Install directory: ${CREATE_DIR_MODELS}")
file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/build/bin/${CREATE_DIR_MODELS})

message(STATUS "Install directory: ${CREATE_DIR_INSTANCES}")
file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/build/bin/${CREATE_DIR_INSTANCES})

add_custom_command(TARGET HeurLpBased PRE_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_SOURCE_DIR}/models/ $<TARGET_FILE_DIR:HeurLpBased>/models)
add_custom_command(TARGET HeurLpBased PRE_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_SOURCE_DIR}/logs/ $<TARGET_FILE_DIR:HeurLpBased>/logs)
add_custom_command(TARGET HeurLpBased PRE_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_SOURCE_DIR}/instances/ $<TARGET_FILE_DIR:HeurLpBased>/instances)