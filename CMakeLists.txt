project(lodev CXX)

find_package(SDL REQUIRED)
include_directories(${SDL_INCLUDE_DIRS})

add_library(quickcg
  files/quickcg/quickcg.cpp)

target_include_directories(quickcg PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/files/quickcg)
target_link_directories(quickcg PRIVATE)

add_executable(raycaster_flat
  files/raycaster_flat.cpp)
target_include_directories(raycaster_flat PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/files/quickcg)
target_link_libraries(raycaster_flat PRIVATE quickcg)
target_link_libraries(raycaster_flat PRIVATE ${SDL_LIBRARIES})

add_executable(raycaster_textured
  files/raycaster_textured.cpp)
target_include_directories(raycaster_textured PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/files/quickcg)
target_link_libraries(raycaster_textured PRIVATE quickcg)
target_link_libraries(raycaster_textured PRIVATE ${SDL_LIBRARIES})


add_executable(raycaster_floor
  files/raycaster_floor.cpp)
target_include_directories(raycaster_floor PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/files/quickcg)
target_link_libraries(raycaster_floor PRIVATE quickcg)
target_link_libraries(raycaster_floor PRIVATE ${SDL_LIBRARIES})

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/download.cmake 
  "file(DOWNLOAD https://lodev.org/cgtutor/files/wolftex.zip ${CMAKE_CURRENT_BINARY_DIR}/wolftex.zip)"
)

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/extract.cmake 
  "file(ARCHIVE_EXTRACT INPUT ${CMAKE_CURRENT_BINARY_DIR}/wolftex.zip DESTINATION ${CMAKE_CURRENT_BINARY_DIR})"
)

add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/pics/eagle.png
  COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/download.cmake
  COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/extract.cmake
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
  VERBATIM
)
add_custom_target(textures DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/pics/eagle.png)

set_target_properties(textures
  PROPERTIES
  ADDITIONAL_CLEAN_FILES ${CMAKE_CURRENT_BINARY_DIR}/pics)

add_dependencies(raycaster_floor textures)






















if (0)
add_custom_command(TARGET raycaster_floor PRE_BUILD COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_CURRENT_BINARY_DIR}/pics)

file(GLOB ConfigFiles ${CMAKE_SOURCE_DIR}/images/*.png)

foreach(ConfigFile ${ConfigFiles})
  add_custom_command(TARGET raycaster_floor POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${ConfigFile}"
    "${CMAKE_CURRENT_BINARY_DIR}/pics"
  DEPENDS raycaster_floor)
endforeach()
endif()
