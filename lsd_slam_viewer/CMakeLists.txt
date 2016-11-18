cmake_minimum_required(VERSION 2.4.6)
project(lsd_slam_viewer)

if(COMMAND cmake_policy)
      cmake_policy(SET CMP0003 NEW)
    endif(COMMAND cmake_policy)

# Set the build type. Options are:
#  Coverage : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug : w/ debug symbols, w/o optimization
#  Release : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel : w/o debug symbols, w/ optimization, stripped binaries
set(CMAKE_BUILD_TYPE Debug)

ADD_SUBDIRECTORY(${PROJECT_SOURCE_DIR}/thirdparty/Sophus)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake)

find_package(cmake_modules REQUIRED)
find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  dynamic_reconfigure
  sensor_msgs
  roscpp
  rosbag
  message_generation
  roslib
)

add_message_files(DIRECTORY msg FILES keyframeMsg.msg keyframeGraphMsg.msg)

generate_messages(
   DEPENDENCIES 
   std_msgs
   )

generate_dynamic_reconfigure_options(
  cfg/LSDSLAMViewerParams.cfg
)

catkin_package()

find_package(Qt4 REQUIRED COMPONENTS QtCore QtGui)
find_package(OpenGL REQUIRED)
set(QT_USE_QTOPENGL TRUE)
set(QT_USE_QTXML TRUE)
find_package(QGLViewer REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(OpenCV REQUIRED)
find_package(Boost REQUIRED COMPONENTS thread)

include_directories(
        ${CMAKE_CURRENT_BINARY_DIR}
        ${QGLVIEWER_INCLUDE_DIR}
		    ${catkin_INCLUDE_DIRS} 
		    ${EIGEN3_INCLUDE_DIR}
		    ${QT_INCLUDES} )

# SSE flags
set(CMAKE_CXX_FLAGS
   "${CMAKE_CXX_FLAGS} -march=native -Wall -std=c++0x"
)

# Sources files
set(SOURCE_FILES         
  src/PointCloudViewer.cpp
  src/KeyFrameDisplay.cpp
  src/KeyFrameGraphDisplay.cpp
  src/settings.cpp
)

set(HEADER_FILES     
  src/PointCloudViewer.h
  src/KeyFrameDisplay.h
  src/KeyFrameGraphDisplay.h
  src/settings.h
)

include_directories(
  ${PROJECT_SOURCE_DIR}/thirdparty/Sophus
)  

add_executable(viewer src/main_viewer.cpp ${SOURCE_FILES} ${HEADER_FILES})

## Add cmake target dependencies of the executable/library
## message headers need to be generated before nodes
add_dependencies(viewer lsd_slam_viewer_generate_messages_cpp)

target_link_libraries(viewer ${QGLViewer_LIBRARIES}
			     ${QGLVIEWER_LIBRARY} 
			     ${catkin_LIBRARIES}
			     ${Boost_LIBRARIES}
			     ${QT_LIBRARIES}
			     GL glut GLU
		     )

#add_executable(videoStitch src/main_stitchVideos.cpp)
#target_link_libraries(viewer ${QGLViewer_LIBRARIES}
#			     ${QGLVIEWER_LIBRARY}
#			     ${catkin_LIBRARIES}
#			     ${QT_LIBRARIES}
#			     GL glut GLU
#		      )

