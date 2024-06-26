cmake_minimum_required(VERSION 3.15)
cmake_policy(SET CMP0091 NEW)
cmake_policy(SET CMP0042 NEW)

project (jpeg-turbo)

# define NPI_VERSION
add_definitions(-DNAPI_VERSION=7)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

include_directories(${CMAKE_JS_INC})

set(HEADER_FILES
  "src/buffersize.h"
  "src/compress.h"
  "src/consts.h"
  "src/decompress.h"
  "src/enums.h"
  "src/util.h"
)
set(SOURCE_FILES
  "src/buffersize.cc"
  "src/compress.cc"
  "src/decompress.cc"
  "src/enums.cc"
  "src/util.cc"
  "src/exports.cc"
)

option(WITH_JPEG8 "" 1)
option(ENABLE_SHARED "" 0)
add_subdirectory("deps/libjpeg-turbo")

add_library(${PROJECT_NAME} SHARED ${SOURCE_FILES} ${HEADER_FILES} ${CMAKE_JS_SRC})
set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")
target_include_directories(${PROJECT_NAME} PRIVATE "deps/libjpeg-turbo")
target_link_libraries(${PROJECT_NAME} ${CMAKE_JS_LIB} turbojpeg-static)

if(MSVC AND CMAKE_JS_NODELIB_DEF AND CMAKE_JS_NODELIB_TARGET)
  # Generate node.lib
  execute_process(COMMAND ${CMAKE_AR} /def:${CMAKE_JS_NODELIB_DEF} /out:${CMAKE_JS_NODELIB_TARGET} ${CMAKE_STATIC_LINKER_FLAGS})
endif()
