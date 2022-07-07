cmake_minimum_required(VERSION 3.17)
project(lg_imgui)

include(lg_cmake_utils/lg_cmake_utils.cmake)

set(CMAKE_CXX_STANDARD 20)

add_subdirectory(external/pybind11)


####################################################
# Build imgui Bound C++ library
####################################################
# Note: any sub shared library used by the bound C++ library should install its targets into the "lib/" subfolder
set(imgui_dir ${CMAKE_CURRENT_LIST_DIR}/external/imgui)
file(GLOB imgui_sources ${imgui_dir}/*.h ${imgui_dir}/*.cpp  ${imgui_dir}/misc/cpp/*.cpp ${imgui_dir}/misc/cpp/*.h)
add_library(imgui SHARED ${imgui_sources} bindings/imgui_docking_internal_types.h)
target_include_directories(imgui PUBLIC ${imgui_dir} ${CMAKE_CURRENT_LIST_DIR})
target_compile_definitions(imgui PRIVATE IMGUI_USER_CONFIG="litgen_imconfig.h")
install(TARGETS imgui DESTINATION ./lib/)


#########################################################################
# Build python module that provides bindings to the library implot
#########################################################################
set(bound_library imgui)                 # The library for which we are building bindings
set(python_native_module_name _lg_imgui) # This is the native python module name
set(python_wrapper_module_name lg_imgui) # This is the python wrapper around the native module
set(python_module_sources bindings/module.cpp bindings/pybind_${bound_library}.cpp) # native python module sources

pybind11_add_module(${python_native_module_name} ${python_module_sources})
lg_setup_module(
    ${bound_library}
    ${python_native_module_name}
    ${python_wrapper_module_name}
)
