cmake_minimum_required(VERSION 3.14)
project(Gberry_CAM CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Define source directories
set(SRC_DIR ${CMAKE_SOURCE_DIR})

# version.cpp.in 처리
configure_file(${SRC_DIR}/core/version.cpp.in ${CMAKE_BINARY_DIR}/version.cpp @ONLY)

# Collect sources
set(SOURCES
    ${SRC_DIR}/apps/rpicam_still.cpp
    ${SRC_DIR}/core/buffer_sync.cpp
    ${SRC_DIR}/core/dma_heaps.cpp
    ${SRC_DIR}/core/rpicam_app.cpp
    ${SRC_DIR}/core/options.cpp
    ${SRC_DIR}/core/post_processor.cpp
    ${SRC_DIR}/post_processing_stages/histogram.cpp
    ${SRC_DIR}/post_processing_stages/post_processing_stage.cpp
    ${SRC_DIR}/post_processing_stages/pwl.cpp
    ${SRC_DIR}/encoder/encoder.cpp
    ${SRC_DIR}/encoder/h264_encoder.cpp
    ${SRC_DIR}/encoder/mjpeg_encoder.cpp
    ${SRC_DIR}/encoder/null_encoder.cpp
    ${SRC_DIR}/output/circular_output.cpp
    ${SRC_DIR}/output/file_output.cpp
    ${SRC_DIR}/output/net_output.cpp
    ${SRC_DIR}/output/output.cpp
    ${SRC_DIR}/image/bmp.cpp
    ${SRC_DIR}/image/dng.cpp
    ${SRC_DIR}/image/jpeg.cpp
    ${SRC_DIR}/image/png.cpp
    ${SRC_DIR}/image/yuv.cpp
    ${SRC_DIR}/preview/null_preview.cpp
    ${SRC_DIR}/preview/preview.cpp
    ${CMAKE_BINARY_DIR}/version.cpp
)

# Find required packages
find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBCAMERA REQUIRED libcamera)
pkg_check_modules(DRM REQUIRED libdrm)
pkg_check_modules(EGL REQUIRED egl)
pkg_check_modules(EXIF REQUIRED libexif)
pkg_check_modules(JPEG REQUIRED libjpeg)
pkg_check_modules(TIFF REQUIRED libtiff-4)
pkg_check_modules(PNG REQUIRED libpng)

find_package(Boost REQUIRED COMPONENTS program_options)

# Executable target
add_executable(rpicam_still ${SOURCES})

# Include directories
target_include_directories(rpicam_still PRIVATE
    ${SRC_DIR}
    ${Boost_INCLUDE_DIRS}
    ${LIBCAMERA_INCLUDE_DIRS}
    ${DRM_INCLUDE_DIRS}
    ${EGL_INCLUDE_DIRS}
    ${EXIF_INCLUDE_DIRS}
    ${JPEG_INCLUDE_DIRS}
    ${TIFF_INCLUDE_DIRS}
    ${PNG_INCLUDE_DIRS}
)

# Link libraries
target_link_libraries(rpicam_still PRIVATE
    ${Boost_LIBRARIES}
    ${LIBCAMERA_LIBRARIES}
    ${DRM_LIBRARIES}
    ${EGL_LIBRARIES}
    ${EXIF_LIBRARIES}
    ${JPEG_LIBRARIES}
    ${TIFF_LIBRARIES}
    ${PNG_LIBRARIES}
    pthread
    dl
    # m
)

# Definitions for conditional features (can extend here if needed)
add_definitions(-DENABLE_LIBAV=1 -DENABLE_DRM=1 -DENABLE_EGL=1 -DENABLE_QT=1)