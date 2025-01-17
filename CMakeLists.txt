cmake_minimum_required(VERSION 3.14)
include(FetchContent)

project(H5WASM
    DESCRIPTION "High level HDF5 read/write library"
    LANGUAGES CXX C
)

FetchContent_Declare(
  libhdf5-wasm
#  URL file:///home/brian/dev/libhdf5-wasm/libhdf5-wasm.tar.gz
   URL https://github.com/usnistgov/libhdf5-wasm/releases/download/v0.3.0_3.1.28/libhdf5-1_12_2-wasm.tar.gz
   URL_HASH SHA256=7089f9bf29dc3759d7aa77848cfa12d546eabd152d40dd00a90aace99c056600
)
FetchContent_MakeAvailable(libhdf5-wasm)

add_executable(hdf5_util src/hdf5_util.cc)
target_link_libraries(hdf5_util hdf5-wasm)

# Optional flags to set when building your project
set_target_properties(hdf5_util PROPERTIES
    LINK_FLAGS "-O3 --bind  \
    -lidbfs.js \
    -lworkerfs.js \
    -s MAIN_MODULE=2 \
    -s ALLOW_TABLE_GROWTH=1 \
    -s ALLOW_MEMORY_GROWTH=1 \
    -s WASM_BIGINT \
    -s ENVIRONMENT=web,worker \
    -s SINGLE_FILE \
    -s EXPORT_ES6=1 \
    -s FORCE_FILESYSTEM=1 \
    -s USE_ZLIB=1 \
    -s EXPORTED_RUNTIME_METHODS=\"['ccall', 'cwrap', 'FS', 'AsciiToString', 'UTF8ToString']\" \
    -s EXPORTED_FUNCTIONS=\"['_H5Fopen', '_H5Fclose', '_H5Fcreate', '_malloc', '_free', '_stderr', '_memset', '_memcpy']\""
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/dist/esm
    RUNTIME_OUTPUT_NAME hdf5_util
    POSITION_INDEPENDENT_CODE ON
)

add_executable(hdf5_util_node src/hdf5_util.cc)
target_link_libraries(hdf5_util_node hdf5-wasm)
set_target_properties(hdf5_util_node PROPERTIES
    LINK_FLAGS "-O3 --bind  \
    -s NODEJS_CATCH_EXIT=0 \
    -s NODEJS_CATCH_REJECTION=0 \
    -s ALLOW_TABLE_GROWTH=1 \
    -s ALLOW_MEMORY_GROWTH=1 \
    -s WASM_BIGINT \
    -s NODERAWFS=1 \
    -s FORCE_FILESYSTEM=1 \
    -s ENVIRONMENT=node \
    -s SINGLE_FILE \
    -s EXPORT_ES6=1 \
    -s USE_ZLIB=1 \
    -s ASSERTIONS=1 \
    -s EXPORTED_RUNTIME_METHODS=\"['ccall', 'cwrap', 'FS', 'AsciiToString', 'UTF8ToString']\" \
    -s EXPORTED_FUNCTIONS=\"['_H5Fopen', '_H5Fclose', '_H5Fcreate', '_malloc', '_free', '_stderr', '_memset', '_memcpy']\""
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/dist/node
    RUNTIME_OUTPUT_NAME hdf5_util
    POSITION_INDEPENDENT_CODE ON
)
