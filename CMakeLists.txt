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

set(local_custom_Pangolin_INSTALL_DIR _installed_Pangolin)
set(custom_Pangolin_BUILD_NAME g-build)

find_package(Eigen3 REQUIRED)

find_package(
    Boost
    COMPONENTS program_options timer filesystem serialization
    REQUIRED)

#find_package(Pangolin REQUIRED)

set(ICPCUDA_PROJECT_PATH ${PROJECT_SOURCE_DIR}/third-party/ICPCUDA)
set(ICPCUDA_LIBS ${ICPCUDA_PROJECT_PATH}/${custom_Pangolin_BUILD_NAME}/libICP.so)
set(ICPCUDA_third_party ${ICPCUDA_PROJECT_PATH}/third-party)

set(Pangolin_INSTALL_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/${custom_Pangolin_BUILD_NAME}/${local_custom_Pangolin_INSTALL_DIR}" CACHE PATH "Pangolin install directory")
set(Pangolin_INCLUDE_DIRS "${Pangolin_INSTALL_DIR}/include")


#set(Pangolin_INCLUDE_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/third-party/Pangolin/include")
set(Sophus_INCLUDE_DIRS ${ICPCUDA_third_party}/Sophus)

set(ICPCUDA_INCLUDE_DIRS "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/include")

include(ExternalProject)

ExternalProject_Add(icpCuda
    SOURCE_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA"
    BINARY_DIR "${PROJECT_SOURCE_DIR}/third-party/ICPCUDA/${custom_Pangolin_BUILD_NAME}"
    CMAKE_ARGS -DPangolin_LOCAL_INSTALL_DIR=${local_custom_Pangolin_INSTALL_DIR}
    -DPangolin_BUILD_NAME=${custom_Pangolin_BUILD_NAME}
    INSTALL_COMMAND "")

find_package(CUDA REQUIRED)

add_executable(main main.cpp)
add_dependencies(main icpCuda)

set(Pangolin_LIBRARIES ${Pangolin_INSTALL_DIR}/lib/libpangolin.so)

target_link_libraries(main
    ${ICPCUDA_LIBS}
    ${Pangolin_LIBRARIES}
    ${CUDA_LIBRARIES}
    )

target_include_directories(main
    PUBLIC
    ${Sophus_INCLUDE_DIRS}
    ${EIGEN3_INCLUDE_DIRS}
    ${ICPCUDA_INCLUDE_DIRS}
    ${Pangolin_INCLUDE_DIRS}
    )