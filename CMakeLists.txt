cmake_minimum_required(VERSION 3.12)

project(<BASENAME> Fortran)

include(GNUInstallDirs)

# set CMAKE_PREFIX_PATH to the directory containing iriclibConfig.cmake
# ie -DCMAKE_PREFIX_PATH=c:/iricdev/lib/install/iricdev-0.2.9/lib/cmake/iriclib
find_package(iriclib)

add_executable(<BASENAME>
  <ADD SOURCE FILES HERE>
)

target_link_libraries(<BASENAME> iriclib::iriclib)

install(TARGETS <BASENAME> DESTINATION "${CMAKE_INSTALL_BINDIR}")

install(FILES definition.xml DESTINATION "${CMAKE_INSTALL_BINDIR}")

# include(CTest)

# add_subdirectory(tests)
