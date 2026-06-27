cmake_minimum_required(VERSION 3.5)

set(CMAKE_C_STANDARD 99)

project(wb32-dfu-updater_cli VERSION 1.0.0)

if (NOT (CMAKE_BUILD_TYPE STREQUAL Release))
    #   Build type Debug / Release
    set(CMAKE_BUILD_TYPE Debug)
    #   Build option
    add_compile_options("-Wall") 
endif()

#   Add head file path.
include_directories(${CMAKE_SOURCE_DIR})
include_directories(${PROJECT_BINARY_DIR})
include_directories(${CMAKE_SOURCE_DIR}/src_common)
if (WIN32)
    include_directories(${CMAKE_SOURCE_DIR}/src_windows)
endif()

#   Add source file.
aux_source_directory(${CMAKE_SOURCE_DIR}/src_common SRC_FILES)
if (WIN32)
    aux_source_directory(${CMAKE_SOURCE_DIR}/src_windows SRC_FILES)
endif()

include(${PROJECT_SOURCE_DIR}/cmake/compiler_conf.cmake)
include(${PROJECT_SOURCE_DIR}/cmake/building_output.cmake)
configure_file(${PROJECT_SOURCE_DIR}/cmake/config.h.in cmake_config.h)


#   Discover libusb-1.0.
#
#   By default we let pkg-config locate the library; this is the portable,
#   standard mechanism and works out of the box on Linux, macOS (Homebrew) and
#   MSYS2/MinGW. Packagers or users with libusb in a non-standard location can
#   still override discovery by passing -DLIBUSB_INCLUDE_DIRS and
#   -DLIBUSB_LIBRARIES on the cmake command line.
add_library(libusb INTERFACE)

if (LIBUSB_INCLUDE_DIRS AND LIBUSB_LIBRARIES)

    message(STATUS "libusb: using user-provided paths")
    message(STATUS "  LIBUSB_INCLUDE_DIRS: ${LIBUSB_INCLUDE_DIRS}")
    message(STATUS "  LIBUSB_LIBRARIES:    ${LIBUSB_LIBRARIES}")

    target_include_directories(libusb INTERFACE ${LIBUSB_INCLUDE_DIRS})
    target_link_libraries(libusb INTERFACE ${LIBUSB_LIBRARIES})

else ()

    find_package(PkgConfig REQUIRED)
    pkg_check_modules(LIBUSB IMPORTED_TARGET libusb-1.0)

    if (NOT LIBUSB_FOUND)
        message(FATAL_ERROR
            "libusb-1.0 was not found by pkg-config.\n"
            "Install the libusb-1.0 development files, for example:\n"
            "  Debian/Ubuntu : sudo apt install libusb-1.0-0-dev pkg-config\n"
            "  Fedora        : sudo dnf install libusb1-devel pkgconf-pkg-config\n"
            "  Arch          : sudo pacman -S libusb pkgconf\n"
            "  macOS         : brew install libusb pkg-config\n"
            "  MSYS2/MinGW64 : pacman -S mingw-w64-x86_64-libusb mingw-w64-x86_64-pkgconf\n"
            "If libusb is in a custom prefix, point pkg-config at it with "
            "PKG_CONFIG_PATH, or pass -DLIBUSB_INCLUDE_DIRS and -DLIBUSB_LIBRARIES.")
    endif ()

    message(STATUS "libusb: found ${LIBUSB_VERSION} via pkg-config")

    target_link_libraries(libusb INTERFACE PkgConfig::LIBUSB)

endif ()

#   Set execute file path.
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/build/bin)

#   Compile exe
add_executable(${CMAKE_PROJECT_NAME} ${SRC_FILES})

#  include headers and link libusb (handled by the libusb interface target).
target_link_libraries(${CMAKE_PROJECT_NAME} PRIVATE libusb)

#  install configuration.
include(${PROJECT_SOURCE_DIR}/cmake/install_common.cmake)

include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_DIRECTORY ${PROJECT_SOURCE_DIR}/package)
set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/LICENSE")
set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")
set(PROJECT_VERSION_FULL ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH})
set(CPACK_SOURCE_IGNORE_FILES
        ${PROJECT_SOURCE_DIR}/.DS_Store
        ${PROJECT_SOURCE_DIR}/.vscode
        ${PROJECT_SOURCE_DIR}/.git
        ${PROJECT_SOURCE_DIR}/*.hex
        ${PROJECT_SOURCE_DIR}/*.bin
        ${PROJECT_SOURCE_DIR}/*.exe
        ${PROJECT_SOURCE_DIR}/package
        ${PROJECT_SOURCE_DIR}/driver
        ${PROJECT_SOURCE_DIR}/build
        ${PROJECT_SOURCE_DIR}/docs
        ${PROJECT_SOURCE_DIR}/bin)

if(WIN32 AND NOT UNIX)
    set(CPACK_NSIS_DISPLAY_NAME "${CPACK_PACKAGE_INSTALL_DIRECTORY} ${CMAKE_PROJECT_NAME}")
    set(CPACK_NSIS_MODIFY_PATH ON)
endif()


set(CPACK_SOURCE_GENERATOR "TGZ")
set(CPACK_PACKAGE_FILE_NAME ${CMAKE_PROJECT_NAME})
set(CPACK_PACKAGE_INSTALL_DIRECTORY ${CMAKE_PROJECT_NAME})
set(CPACK_PACKAGE_INSTALL_REGISTRY_KEY ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION_FULL})
set(CPACK_SOURCE_PACKAGE_FILE_NAME ${CMAKE_PROJECT_NAME}-v${PROJECT_VERSION_FULL}-${CMAKE_SYSTEM_NAME})
set(CPACK_PACKAGE_EXECUTABLES "${CPACK_SOURCE_PACKAGE_FILE_NAME}" "${CPACK_SOURCE_PACKAGE_FILE_NAME}")

include(CPack)
