set(TARGET vendor-hash)

if (COMMAND llama_add_compile_flags)
    llama_add_compile_flags()
endif()

set(VENDOR_SRCS
    sha256/sha256.c
)

add_library(${TARGET} STATIC
    hash.cpp
    hash.h
    ${VENDOR_SRCS}
)

target_compile_features(${TARGET} PRIVATE cxx_std_17)
set_target_properties(${TARGET} PROPERTIES POSITION_INDEPENDENT_CODE ON)

# disable warnings in 3rd party code, but keep them for hash.cpp
if (CMAKE_C_COMPILER_ID STREQUAL "MSVC")
    set(NO_WARN_FLAG /w)
else()
    set(NO_WARN_FLAG -w)
endif()
set_source_files_properties(${VENDOR_SRCS} PROPERTIES COMPILE_OPTIONS ${NO_WARN_FLAG})

# sha256.c includes "rotate-bits/rotate-bits.h", so consumers get this dir too
target_include_directories(${TARGET} PUBLIC .)
