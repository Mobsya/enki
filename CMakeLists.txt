cmake_minimum_required(VERSION 2.8.11)

project(Enki)

# additional CMake modules
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/CMakeModules)

enable_testing()

find_package(Qt5 COMPONENTS Core Gui Widgets OpenGL)
find_package(OpenGL REQUIRED)

# check for SDL2
find_package(SDL2)

# library version
set(LIB_INSTALL_DIR lib CACHE FILEPATH "Where to install libraries")
set(LIB_VERSION_MAJOR 2) # Must be bumped for incompatible ABI changes
set(LIB_VERSION_MINOR 0)
set(LIB_VERSION_PATCH 0)
set(LIB_VERSION_STRING ${LIB_VERSION_MAJOR}.${LIB_VERSION_MINOR}.${LIB_VERSION_PATCH})

add_subdirectory(enki)

if (Qt5Widgets_FOUND AND Qt5OpenGL_FOUND)
	add_subdirectory(viewer)
endif()

if(ENKI_BUILD_TESTS)
    add_subdirectory(tests)
endif()
if(ENKI_BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()

# generate configure file
configure_file(enkiConfig.cmake.in "${PROJECT_BINARY_DIR}/enkiConfig.cmake" @ONLY)
if (WIN32 AND NOT CYGWIN)
	set(INSTALL_CMAKE_DIR CMake)
else()
	if (APPLE)
		set(INSTALL_CMAKE_DIR Frameworks/enki.framework/Resources/CMake/)
	else()
		set(INSTALL_CMAKE_DIR share/enki/CMake)
	endif()
endif()
install(FILES "${PROJECT_BINARY_DIR}/enkiConfig.cmake" DESTINATION "${INSTALL_CMAKE_DIR}" COMPONENT dev)
