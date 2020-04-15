cmake_minimum_required(VERSION 3.10)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

project(MSOfficeCrypt VERSION 0.1 LANGUAGES C CXX)

string(APPEND MSOfficeCrypt_compiler_flags
    "$<$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>:"
        "-Wall;-Wextra;-Wformat=2;-Wcast-qual;-Wcast-align;-Wwrite-strings;-Wfloat-equal;-Wpointer-arith;"
        "-D_FILE_OFFSET_BITS=64;-msse2;"
    ">")

string(APPEND MSOfficeCrypt_compiler_flags_cxx
    "$<$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>:"
        "-fno-operator-names;"
    ">")

string(APPEND MSOfficeCrypt_compiler_flags
    "$<$<CONFIG:Release>:$<$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>:"
    "-Ofast;-fomit-frame-pointer;-march=native;-msse4;"
    ">>"
)

string(APPEND MSOfficeCrypt_compiler_flags
    "$<$<CONFIG:Debug>:$<$<OR:$<CXX_COMPILER_ID:GNU>,$<CXX_COMPILER_ID:Clang>>:"
    "-g;"
    ">>"
)

find_package(OpenSSL REQUIRED)
find_package(Threads REQUIRED)

add_library(msoc SHARED src/msocdll.cpp)
target_compile_options(msoc PUBLIC ${MSOfficeCrypt_compiler_flags} ${MSOfficeCrypt_compiler_flags_cxx})
target_include_directories(msoc PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})
target_link_libraries(msoc PRIVATE OpenSSL::Crypto)

add_executable(msoffice-crypt src/msoffice-crypt.cpp)
target_compile_options(msoffice-crypt PUBLIC ${MSOfficeCrypt_compiler_flags} ${MSOfficeCrypt_compiler_flags_cxx})
target_include_directories(msoffice-crypt PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})
target_link_libraries(msoffice-crypt PRIVATE msoc OpenSSL::Crypto)

add_executable(attack src/attack.cpp)
target_compile_options(attack PUBLIC ${MSOfficeCrypt_compiler_flags} ${MSOfficeCrypt_compiler_flags_cxx})
target_include_directories(attack PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})
target_link_libraries(attack PRIVATE msoc OpenSSL::Crypto Threads::Threads)

add_executable(cfb_test test/cfb_test.cpp)
target_compile_options(cfb_test PUBLIC ${MSOfficeCrypt_compiler_flags} ${MSOfficeCrypt_compiler_flags_cxx})
target_include_directories(cfb_test PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})

add_executable(hash_test test/hash_test.cpp)
target_compile_options(hash_test PUBLIC ${MSOfficeCrypt_compiler_flags} ${MSOfficeCrypt_compiler_flags_cxx})
target_include_directories(hash_test PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})
target_link_libraries(hash_test PRIVATE OpenSSL::Crypto)

add_executable(minisample src/minisample.c)
target_compile_options(minisample PUBLIC ${MSOfficeCrypt_compiler_flags})
target_include_directories(minisample PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})
target_link_libraries(minisample PRIVATE msoc OpenSSL::Crypto)
SET_TARGET_PROPERTIES(minisample PROPERTIES LINKER_LANGUAGE C)

file(COPY build/compile_commands.json DESTINATION ${PROJECT_SOURCE_DIR})