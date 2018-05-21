cmake_minimum_required(VERSION 2.8.3)
project(image_transport)

find_package(catkin REQUIRED
  COMPONENTS
    message_filters
    pluginlib
    rosconsole
    roscpp
    roslib
    sensor_msgs
    std_srvs
)

find_package(Boost REQUIRED)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  DEPENDS message_filters pluginlib rosconsole roscpp roslib sensor_msgs std_srvs
)

include_directories(include ${catkin_INCLUDE_DIRS})

# Build libimage_transport
add_library(${PROJECT_NAME}
  src/camera_common.cpp
  src/camera_publisher.cpp
  src/camera_subscriber.cpp
  src/image_transport.cpp
  src/publisher.cpp
  src/single_subscriber_publisher.cpp
  src/subscriber.cpp
)
add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME}
  ${Boost_LIBRARIES}
  ${catkin_LIBRARIES}
)

# Build libimage_transport_plugins                                      
add_library(${PROJECT_NAME}_plugins src/manifest.cpp src/raw_publisher.cpp)
target_link_libraries(${PROJECT_NAME}_plugins ${PROJECT_NAME})

install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_plugins
        DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
        COMPONENT main
)
install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

# add three execs
add_executable(republish src/republish.cpp)
target_link_libraries(republish ${PROJECT_NAME})

# add three execs
add_executable(republish_toggle src/republish_toggle.cpp)
target_link_libraries(republish_toggle ${PROJECT_NAME})

add_executable(list_transports src/list_transports.cpp)
target_link_libraries(list_transports
  ${PROJECT_NAME}
  ${catkin_LIBRARIES}
)

install(TARGETS list_transports republish republish_toggle
        DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# add xml file
install(FILES default_plugins.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
