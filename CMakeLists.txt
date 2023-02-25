cmake_minimum_required(VERSION 3.15)

project(common VERSION 1.0.0
               DESCRIPTION "Very nice project"
               LANGUAGES CXX)

               # For access to standard installation directory variables (CMAKE_INSTALL_xDIR).
include(GNUInstallDirs)

add_library(common SHARED src/array.cpp include/array.hpp)

# Set the include directories
target_include_directories(common PRIVATE 
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)
set_target_properties(${PROJECT_NAME} PROPERTIES VERSION ${PROJECT_VERSION})
# Set the shared object version property to the project's major version.
set_target_properties(${PROJECT_NAME} PROPERTIES SOVERSION ${PROJECT_VERSION_MAJOR})
# Set the public header property to the one with the actual API.
set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER include/array.hpp)

# Set any additional compiler flags you need for your library
target_compile_options(common PRIVATE
-Wall
-Wextra
-pedantic
-Werror
-Wconversion
-std=c++17
-Wshadow
)

# Set additional compiler flags specific to a build type
if(CMAKE_BUILD_TYPE MATCHES "Debug")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0 -fno-omit-frame-pointer" )
elseif(CMAKE_BUILD_TYPE MATCHES "Release")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")
else()
    message(FATAL_ERROR "Unknown build type: ${CMAKE_BUILD_TYPE}")
endif()

# Set library shared object and API header file to install.
install(TARGETS ${PROJECT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# Create the pkg-config file from the template.
configure_file(src/${PROJECT_NAME}.pc.in ${PROJECT_NAME}.pc @ONLY)
# Set pkg-config file to install.
install(FILES ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.pc
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
)