file(MAKE_DIRECTORY  ${Bax2Bam_TestsDir}/bin)

# Generate paths for test data
configure_file(
    ${Bax2Bam_TestsDir}/src/TestData.h.in
    ${Bax2Bam_TestsDir}/src/TestData.h
)
include(files.cmake)
include_directories(
    ${BLASR_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${HDF5_INCLUDE_DIRS}
    ${PacBioBAM_INCLUDE_DIRS}
    ${PBDATA_INCLUDE_DIRS}
    ${PBDATA_ROOT_DIR}
    ${PBIHDF_INCLUDE_DIRS}
    ${ZLIB_INCLUDE_DIRS}
    ${gtest_SOURCE_DIR}/include
    ${gtest_SOURCE_DIR}
#    ${Bax2BamTest_H}
)
add_compile_options("${HTSLIB_CFLAGS}")


# Grab postprimary test source files

set(SOURCES
    ${Bax2BamTest_H}
    ${Bax2BamTest_CPP}
)

# shared CXX flags for src & tests
include(CheckCXXCompilerFlag)

# quash warnings from pbdata
check_cxx_compiler_flag("-Wno-overloaded-virtual" HAS_NO_OVERLOADED_VIRTUAL)
if(HAS_NO_OVERLOADED_VIRTUAL)
    add_compile_options("-Wno-overloaded-virtual")
endif()
check_cxx_compiler_flag("-Wno-unused-private-field" HAS_NO_UNUSED_PRIVATE_FIELD)
if(HAS_NO_UNUSED_PRIVATE_FIELD)
    add_compile_options("-Wno-unused-private-field")
endif()
check_cxx_compiler_flag("-Wno-unused-variable" HAS_NO_UNUSED_VARIABLE)
if(HAS_NO_UNUSED_VARIABLE)
    add_compile_options("-Wno-unused-variable")
endif()
check_cxx_compiler_flag("-Wno-uninitialized" HAS_NO_UNINITIALIZED)
if(HAS_NO_UNINITIALIZED)
    add_compile_options("-Wno-uninitialized")
endif()
# NOTE: -Wno-unused-local-typedefs used to quash clang warnings w/ Boost
check_cxx_compiler_flag("-Wno-unused-local-typedef" HAS_NO_UNUSED_LOCAL_TYPEDEF)
if(HAS_NO_UNUSED_LOCAL_TYPEDEF)
    add_compile_options("-Wno-unused-local-typedef")
endif()

add_executable(test_bax2bam ${SOURCES})
# add_executable(test_bax2bam ${Bax2Bam_TestsDir}/src/test_ccs.cpp)
set_target_properties(test_bax2bam PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${Bax2Bam_TestsDir}/bin
)
if (NOT APPLE)
    set(MY_LIBRT -lrt)
else()
endif()
target_link_libraries(test_bax2bam
    ${Bax2Bam_EXE_LINKER_FLAGS}

    gtest
    gtest_main
    ${PBBAM_LINK_FLAG}
    ${BLASR_LIBRARIES}
    ${PBIHDF_LIBRARIES}
    ${PBDATA_LIBRARIES}
    ${HDF5_CPP_LIBRARIES}
    ${HDF5_LIBRARIES}
    ${PacBioBAM_LIBRARIES}
    ${HTSLIB_LDFLAGS}
    ${ZLIB_LIBRARIES}
    ${CMAKE_THREAD_LIBS_INIT} # quirky pthreads
    ${MY_LIBRT}
    "-ldl"
)
# add_test(test_bax2bam test_bax2bam)
add_test(
    NAME UnitTests
    WORKING_DIRECTORY ${Bax2Bam_TestsDir}/bin
    COMMAND test_bax2bam
)
