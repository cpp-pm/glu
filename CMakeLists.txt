cmake_minimum_required(VERSION 3.2)

option(WITH_GL4ES "Use gl4es OpenGL to OpenGL ES wrapper" OFF)

if(WITH_GL4ES)
    cmake_minimum_required(VERSION 3.9.2)

    include(cmake/HunterGate.cmake)
    HunterGate(
        URL "https://github.com/cpp-pm/hunter/archive/v0.23.245.tar.gz"
        SHA1 "36e7f44ee4b3f8e25683e9d700f05a27ecb0a848"
    )
endif()

project(glu VERSION 9.0.1)

include_directories(
    include
    src/include
    src/libnurbs/internals
    src/libnurbs/interface
    src/libnurbs/nurbtess
)

set(GLU_SRC
    src/libutil/error.c
    src/libutil/glue.c
    src/libutil/mipmap.c
    src/libutil/project.c
    src/libutil/quad.c
    src/libutil/registry.c
    src/libtess/dict.c
    src/libtess/geom.c
    src/libtess/memalloc.c
    src/libtess/mesh.c
    src/libtess/normal.c
    src/libtess/priorityq.c
    src/libtess/render.c
    src/libtess/sweep.c
    src/libtess/tess.c
    src/libtess/tessmono.c
    src/libnurbs/interface/bezierEval.cc
    src/libnurbs/interface/bezierPatch.cc
    src/libnurbs/interface/bezierPatchMesh.cc
    src/libnurbs/interface/glcurveval.cc
    src/libnurbs/interface/glinterface.cc
    src/libnurbs/interface/glrenderer.cc
    src/libnurbs/interface/glsurfeval.cc
    src/libnurbs/interface/incurveeval.cc
    src/libnurbs/interface/insurfeval.cc
    src/libnurbs/internals/arc.cc
    src/libnurbs/internals/arcsorter.cc
    src/libnurbs/internals/arctess.cc
    src/libnurbs/internals/backend.cc
    src/libnurbs/internals/basiccrveval.cc
    src/libnurbs/internals/basicsurfeval.cc
    src/libnurbs/internals/bin.cc
    src/libnurbs/internals/bufpool.cc
    src/libnurbs/internals/cachingeval.cc
    src/libnurbs/internals/ccw.cc
    src/libnurbs/internals/coveandtiler.cc
    src/libnurbs/internals/curve.cc
    src/libnurbs/internals/curvelist.cc
    src/libnurbs/internals/curvesub.cc
    src/libnurbs/internals/dataTransform.cc
    src/libnurbs/internals/displaylist.cc
    src/libnurbs/internals/flist.cc
    src/libnurbs/internals/flistsorter.cc
    src/libnurbs/internals/hull.cc
    src/libnurbs/internals/intersect.cc
    src/libnurbs/internals/knotvector.cc
    src/libnurbs/internals/mapdesc.cc
    src/libnurbs/internals/mapdescv.cc
    src/libnurbs/internals/maplist.cc
    src/libnurbs/internals/mesher.cc
    src/libnurbs/internals/monoTriangulationBackend.cc
    src/libnurbs/internals/monotonizer.cc
    src/libnurbs/internals/mycode.cc
    src/libnurbs/internals/nurbsinterfac.cc
    src/libnurbs/internals/nurbstess.cc
    src/libnurbs/internals/patch.cc
    src/libnurbs/internals/patchlist.cc
    src/libnurbs/internals/quilt.cc
    src/libnurbs/internals/reader.cc
    src/libnurbs/internals/renderhints.cc
    src/libnurbs/internals/slicer.cc
    src/libnurbs/internals/sorter.cc
    src/libnurbs/internals/splitarcs.cc
    src/libnurbs/internals/subdivider.cc
    src/libnurbs/internals/tobezier.cc
    src/libnurbs/internals/trimline.cc
    src/libnurbs/internals/trimregion.cc
    src/libnurbs/internals/trimvertpool.cc
    src/libnurbs/internals/uarray.cc
    src/libnurbs/internals/varray.cc
    src/libnurbs/nurbtess/directedLine.cc
    src/libnurbs/nurbtess/gridWrap.cc
    src/libnurbs/nurbtess/monoChain.cc
    src/libnurbs/nurbtess/monoPolyPart.cc
    src/libnurbs/nurbtess/monoTriangulation.cc
    src/libnurbs/nurbtess/partitionX.cc
    src/libnurbs/nurbtess/partitionY.cc
    src/libnurbs/nurbtess/polyDBG.cc
    src/libnurbs/nurbtess/polyUtil.cc
    src/libnurbs/nurbtess/primitiveStream.cc
    src/libnurbs/nurbtess/quicksort.cc
    src/libnurbs/nurbtess/rectBlock.cc
    src/libnurbs/nurbtess/sampleComp.cc
    src/libnurbs/nurbtess/sampleCompBot.cc
    src/libnurbs/nurbtess/sampleCompRight.cc
    src/libnurbs/nurbtess/sampleCompTop.cc
    src/libnurbs/nurbtess/sampleMonoPoly.cc
    src/libnurbs/nurbtess/sampledLine.cc
    src/libnurbs/nurbtess/searchTree.cc
)

add_library(GLU ${GLU_SRC})

set_property(TARGET GLU PROPERTY CXX_STANDARD 11)

target_compile_definitions(GLU PRIVATE LIBRARYBUILD=1)

if(NOT CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    add_definitions(-fvisibility=hidden)
endif()

if(WITH_GL4ES)
    hunter_add_package(gl4es)
    find_package(gl4es CONFIG REQUIRED)
    target_link_libraries(GLU gl4es::GL)
else()
    find_package(OpenGL REQUIRED)
    target_link_libraries(GLU OpenGL::GL)
endif()

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

set(config_install_dir "${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}")
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")


install(
    DIRECTORY ${CMAKE_SOURCE_DIR}/include/
    DESTINATION ${CMAKE_INSTALL_FULL_INCLUDEDIR}
    FILES_MATCHING PATTERN "*.h*"
)

install(TARGETS GLU
    EXPORT "${targets_export_name}"
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    FRAMEWORK DESTINATION ${CMAKE_INSTALL_LIBDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "cmake/template/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${targets_export_name}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)
