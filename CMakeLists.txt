cmake_minimum_required(VERSION 2.8.3)
project(srdfdom)

include(GNUInstallDirs)

find_package(Boost REQUIRED)
include_directories(SYSTEM ${Boost_INCLUDE_DIR})

find_package(urdfdom_headers REQUIRED)
find_package(PkgConfig)
pkg_check_modules(TinyXML tinyxml)

include_directories(include ${TinyXML_INCLUDE_DIRS} ${urdfdom_headers_INCLUDE_DIRS})

add_library(${PROJECT_NAME} SHARED
  src/model.cpp
  src/srdf_writer.cpp
)
target_link_libraries(${PROJECT_NAME} ${TinyXML_LIBRARIES} ${urdfdom_headers_LIBRARIES})


install(TARGETS ${PROJECT_NAME}
  DESTINATION ${CMAKE_INSTALL_LIBDIR}
)
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
  FILES_MATCHING PATTERN "*.h"
)

install(PROGRAMS 
  scripts/display_srdf
  DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(FILES package.xml DESTINATION
  ${CMAKE_INSTALL_DATAROOTDIR}/${PROJECT_NAME})

set(PKG_DESC "Semantic Robot Description Format")
set(PKG_DEPENDS "urdfdom_headers tinyxml") # make the list separated by spaces instead of ;
set(PKG_URDF_LIBS "-l${PROJECT_NAME}")
set(pkg_conf_file "cmake/pkgconfig/srdfdom.pc")
configure_file("${CMAKE_CURRENT_SOURCE_DIR}/${pkg_conf_file}.in" "${CMAKE_BINARY_DIR}/${pkg_conf_file}" @ONLY)
install(FILES ${CMAKE_BINARY_DIR}/${pkg_conf_file}
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig/ COMPONENT pkgconfig)
