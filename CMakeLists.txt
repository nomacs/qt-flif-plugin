cmake_minimum_required(VERSION 3.1)

# load paths from the user file if exists
if (EXISTS ${CMAKE_SOURCE_DIR}/CMakeUserPaths.cmake)
	# include(${CMAKE_SOURCE_DIR}/CMakeUserPaths.cmake)
	
	 if(NOT QT_QMAKE_EXECUTABLE)
		find_program(QT_QMAKE_EXECUTABLE NAMES "qmake" "qmake-qt5" "qmake.exe")
	 endif()
	 
	 message(STATUS "QMake found: ${QT_QMAKE_EXECUTABLE}")
	 get_filename_component(QT_QMAKE_PATH ${QT_QMAKE_EXECUTABLE} PATH)
	 
	set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} ${QT_QMAKE_PATH}/../lib/cmake/Qt5)
	
	message(STATUS "using user paths, new prefix path: ${CMAKE_PREFIX_PATH}")
elseif(MSVC)
	message(WARNING "Could not find CMakeUserPaths.cmake - please use this file to specify your install directories (see CMakeUserPathsGit.cmake)")
endif()

find_package(ECM 5.19.0  NO_MODULE)
set(CMAKE_MODULE_PATH ${ECM_MODULE_PATH} ${ECM_KDE_MODULE_DIR})

if (NOT MSVC)
    include(KDEInstallDirs)
endif()

find_package(Qt5Gui REQUIRED)

set(CMAKE_AUTOMOC ON)

include_directories(/usr/include/FLIF)
 
add_library(flif MODULE src/FlifPlugin.cpp)
target_link_libraries(flif Qt5::Gui -lflif)

# Turn on warnings
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")

# C++14
set_property(TARGET flif PROPERTY CXX_STANDARD 14)
set_property(TARGET flif PROPERTY CXX_STANDARD_REQUIRED ON)

install(TARGETS flif DESTINATION ${KDE_INSTALL_QTPLUGINDIR}/imageformats/)
install(FILES configuration/qimageioplugins/x-flif.desktop DESTINATION ${KDE_INSTALL_KSERVICES5DIR}/qimageioplugins/)
install(FILES configuration/qimageioplugins/x-flif.desktop DESTINATION ${SERVICES_INSTALL_DIR}/qimageioplugins/)
