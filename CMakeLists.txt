# ======================================================================
#  larlitecvtagger main build file
#
#  cd .../path/to/build/directory
#  source .../path/to/larlitecvtagger/ups/setup_for_development <-d|-p>
#  cmake [-DCMAKE_INSTALL_PREFIX=/install/path]
#        -DCMAKE_BUILD_TYPE=$CETPKG_TYPE
#        $CETPKG_SOURCE
#  make
#  make test
#  make install
#  make package (builds distribution tarfile)
# ======================================================================


# use cmake 2.8 or later
cmake_minimum_required (VERSION 2.8)

project(larlitecvtagger)

# cetbuildtools contains our cmake modules
SET ( CETBUILDTOOLS_VERSION $ENV{CETBUILDTOOLS_VERSION} )
IF (NOT CETBUILDTOOLS_VERSION)
    MESSAGE (FATAL_ERROR "ERROR: setup cetbuildtools to get the cmake modules")
ENDIF()

set(CMAKE_MODULE_PATH $ENV{ART_DIR}/Modules
		      $ENV{CETBUILDTOOLS_DIR}/Modules
		      ${CMAKE_MODULE_PATH})

include(CetCMakeEnv)
cet_cmake_env()

cet_set_compiler_flags(DIAGS CAUTIOUS
  WERROR
  NO_UNDEFINED
  EXTRA_FLAGS -pedantic
  EXTRA_CXX_FLAGS -std=c++11 )

cet_report_compiler_flags()

# these are minimum required versions, not the actual product versions
find_ups_product( larlite v1_00_00 )
find_ups_product( larcv v1_00_00 )
find_ups_product( cetbuildtools v5_06_01 )

execute_process(COMMAND "git submodule update --init"
		WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
file(GLOB larlitecv_source "${CMAKE_CURRENT_SOURCE_DIR}/larlitecv/*")
file(COPY ${larlitecv_source}  DESTINATION ${CMAKE_INSTALL_PREFIX}/${flavorqual_dir})

execute_process(COMMAND "source configure.sh; make"
		WORKING_DIRECTORY ${CMAKE_INSTALL_PREFIX}/${flavorqual_dir})

add_subdirectory(test)

# ups - table and config files
add_subdirectory(ups)

# packaging utility
include(UseCPack)
