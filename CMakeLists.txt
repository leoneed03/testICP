cmake_minimum_required(VERSION 3.10.0)

project(testICPCUDA)

set(CMAKE_CXX_STANDARD 17)
set(GCC_COVERAGE_COMPILE_FLAGS " -Os -march=native")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GCC_COVERAGE_COMPILE_FLAGS}")
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/modules")
#set(GDR_FLAGS "")

#find_package(TBB REQUIRED)
#find_package(METIS REQUIRED)
#find_package(OpenCV REQUIRED)
#find_package(OpenGL REQUIRED)
#find_package(GLUT REQUIRED)
#find_package(GLEW REQUIRED)
#find_package(CUDA REQUIRED)
#find_package(Sophus REQUIRED)
find_package(Eigen3 REQUIRED)

find_package(
    Boost
    COMPONENTS program_options timer filesystem serialization
    REQUIRED)

#find_package(Pangolin REQUIRED)

set(icpCudaPath ${PROJECT_SOURCE_DIR}/third-party/ICPCUDA)
set(icp_LIBS ${icpCudaPath}/gbuild/libICP.so)

set(ICP_third_party ${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/third-party)
set(Pangolin_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/third-party/Pangolin/include")
set(Sophus_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/third-party/Sophus")
set(Pangolin_CUSTOM_INCLUDE_DIR ${ICP_third_party}/Pangolin/gbuild/src/include)
include_directories(${Sophus_INCLUDE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/include)

include_directories(${Pangolin_CUSTOM_INCLUDE_DIR})
include_directories(${Pangolin_INCLUDE_DIR})
#add_subdirectory(${icpCudaPath})
include(ExternalProject)


ExternalProject_Add(icpCuda
    SOURCE_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA"
    BINARY_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/gbuild"
    CONFIGURE_COMMAND cmake .. #-DCMAKE_C_COMPILER=/usr/bin/gcc-6
    INSTALL_COMMAND "")

#if(${DEBUG_PRINT_PROGRESS})
#  list(APPEND GDR_FLAGS "-DDEBUG_PRINT_PROGRESS")
#endif()
#
#set(SIFTGPU_LIBS ${PROJECT_SOURCE_DIR}/siftgpu/bin/libsiftgpu.so)
# set(GTSAM_LIBS ${PROJECT_SOURCE_DIR}/third-party/gtsam/gbuild/lib/libgtsam.a)
# set(GTSAM_INCLUDE_DIRS ${PROJECT_SOURCE_DIR}/third-party/gtsam/gbuild/include)

#set(GDR_HEADER_FILES
#    ${PROJECT_SOURCE_DIR}/include/keyFeatures.h
#    ${PROJECT_SOURCE_DIR}/include/fileProc.h
#    ${PROJECT_SOURCE_DIR}/include/VertexCG.h
#    ${PROJECT_SOURCE_DIR}/include/CorrespondenceGraph.h
#    ${PROJECT_SOURCE_DIR}/include/transformationRt.h
#    ${PROJECT_SOURCE_DIR}/include/cameraRGBD.h
#    ${PROJECT_SOURCE_DIR}/include/siftModule.h
#    ${PROJECT_SOURCE_DIR}/include/groundTruthTransformer.h
#    ${PROJECT_SOURCE_DIR}/include/rotationAveraging.h
#    ${PROJECT_SOURCE_DIR}/include/quaternions.h
#    ${PROJECT_SOURCE_DIR}/include/umeyama.h
#    ${PROJECT_SOURCE_DIR}/include/poseEstimation.h
#    ${PROJECT_SOURCE_DIR}/include/printer.h
#    ${PROJECT_SOURCE_DIR}/include/errors.h)
#
#set(GDR_SOURCE_FILES
#    ${PROJECT_SOURCE_DIR}/src/keyFeatures.cpp
#    ${PROJECT_SOURCE_DIR}/src/fileProc.cpp
#    ${PROJECT_SOURCE_DIR}/src/VertexCG.cpp
#    ${PROJECT_SOURCE_DIR}/src/CorrespondenceGraph.cpp
#    ${PROJECT_SOURCE_DIR}/src/transformationRt.cpp
#    ${PROJECT_SOURCE_DIR}/src/cameraRGBD.cpp
#    ${PROJECT_SOURCE_DIR}/src/siftModule.cpp
#    ${PROJECT_SOURCE_DIR}/src/groundTruthTransformer.cpp
#    ${PROJECT_SOURCE_DIR}/src/rotationAveraging.cpp
#    ${PROJECT_SOURCE_DIR}/src/quaternions.cpp
#    ${PROJECT_SOURCE_DIR}/src/umeyama.cpp
#    ${PROJECT_SOURCE_DIR}/src/poseEstimation.cpp)

#add_library(GDR_LIB SHARED ${GDR_SOURCE_FILES} ${GDR_HEADER_FILES})
#
#add_dependencies(GDR_LIB siftgpu # gtsam
#    )
#
#target_compile_definitions(GDR_LIB PUBLIC ${GDR_FLAGS})
#
#target_link_libraries(
#    GDR_LIB
#    ${CUDA_LIBRARIES}
#    ${SIFTGPU_LIBS}
#    ${OpenCV_LIBS}
#    # ${GTSAM_LIBS}
#    ${Boost_LIBRARIES}
#    ${METIS_LIBRARIES}
#    ${TBB_LIBRARIES})
#
#target_include_directories(
#    GDR_LIB
#    PUBLIC # ${GTSAM_INCLUDE_DIRS}
#    ${PROJECT_SOURCE_DIR}/include ${EIGEN3_INCLUDE_DIRS}
#    ${PROJECT_SOURCE_DIR}/siftgpu/src/SiftGPU
#    PRIVATE ${OpenGL_INCLUDE_DIR})

#include_directories(icpCudaPath)
find_package(CUDA REQUIRED)

include_directories(${EIGEN3_INCLUDE_DIRS})
add_executable(main main.cpp)
add_dependencies(main icpCuda)

set(Pangolin_DIR "${ICP_third_party}/Pangolin/gbuild/src" CACHE PATH "Pangolin build directory")
set(Pangolin_LIBRARIES ${Pangolin_DIR}/libpangolin.so)
target_link_libraries(main ${icp_LIBS} ${Pangolin_LIBRARIES} ${CUDA_LIBRARIES})