cmake_minimum_required(VERSION 3.10)

project(MSOfficeCrypt VERSION 0.1 LANGUAGES CXX)

find_package(OpenSSL REQUIRED)
find_package(Threads REQUIRED)

add_library(msoclib STATIC src/msocdll.cpp)
add_library(msoc SHARED src/msocdll.cpp)

target_include_directories(msoclib PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})
target_include_directories(msoc PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})

target_link_libraries(msoc PRIVATE OpenSSL::Crypto)

add_executable(msoffice-crypt src/msoffice-crypt.cpp)

target_include_directories(msoffice-crypt PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})

target_link_libraries(msoffice-crypt PRIVATE msoclib OpenSSL::Crypto)

add_executable(attack src/attack.cpp)

target_include_directories(attack PRIVATE include PUBLIC cybozulib/include ${OPENSSL_INCLUDE_DIR})

target_link_libraries(attack PRIVATE msoclib OpenSSL::Crypto Threads::Threads)