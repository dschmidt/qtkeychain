cmake_minimum_required(VERSION 2.8)

project(qtkeychain)

find_package(Qt4 COMPONENTS QtCore REQUIRED)
include_directories(${QT_INCLUDES})

list(APPEND qtkeychain_LIBRARIES ${QT_QTCORE_LIBRARY})
set(qtkeychain_SOURCES
    keychain.cpp
)


if(WIN32)
    list(APPEND qtkeychain_SOURCES keychain_win.cpp)
    list(APPEND qtkeychain_LIBRARIES crypt32)
endif()

if(APPLE)
    list(APPEND qtkeychain_SOURCES keychain_mac.cpp)

    find_library(COREFOUNDATION_LIBRARY CoreFoundation)
    list(APPEND qtkeychain_LIBRARIES ${COREFOUNDATION_LIBRARY})

    find_library(SECURITY_LIBRARY Security)
    list(APPEND qtkeychain_LIBRARIES ${SECURITY_LIBRARY})
endif()

if(UNIX AND NOT APPLE)
    list(APPEND qtkeychain_SOURCES keychain_dbus.cpp)
endif()

if(NOT QTKEYCHAIN_STATIC)
    add_library(qtkeychain SHARED ${qtkeychain_SOURCES})
    set_target_properties(qtkeychain PROPERTIES COMPILE_DEFINITIONS QKEYCHAIN_BUILD_QKEYCHAIN_LIB)
    target_link_libraries(qtkeychain ${qtkeychain_LIBRARIES} )
else()
    add_library(qtkeychain STATIC ${qtkeychain_SOURCES})
endif()