cmake_minimum_required(VERSION 3.12)

project(<BASENAME> Fortran)

include(GNUInstallDirs)

# set CMAKE_PREFIX_PATH to the directory containing iriclibConfig.cmake
# ie -DCMAKE_PREFIX_PATH=c:/iricdev/lib/install/iriclib-0.2.9/lib/cmake/iriclib
find_package(iriclib REQUIRED)

# Change this to ON if solver requires OpenMP
option(IRIC_SOLVER_USE_OPENMP "Solver requires OpenMP" OFF)
if(IRIC_SOLVER_USE_OPENMP)
  find_package(OpenMP REQUIRED)
endif()

add_executable(<BASENAME>
  <ADD SOURCE FILES HERE>
)

target_link_libraries(<BASENAME> PRIVATE iriclib::iriclib)

if(IRIC_SOLVER_USE_OPENMP AND OpenMP_Fortran_FOUND)
  target_link_libraries(<BASENAME> PRIVATE OpenMP::OpenMP_Fortran)
endif()

install(TARGETS <BASENAME> DESTINATION "${CMAKE_INSTALL_BINDIR}")

install(FILES <LOCATION OF definition.xml> DESTINATION "${CMAKE_INSTALL_BINDIR}")

# include(CTest)

# add_subdirectory(tests)
