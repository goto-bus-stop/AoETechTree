cmake_minimum_required(VERSION 3.9)

set(CMAKE_SYSTEM_NAME Windows)
set(CMAKE_CXX_STANDARD 17)

if (UNIX)
  set(CMAKE_SHARED_LIBRARY_LINK_C_FLAGS "")
  set(CMAKE_SHARED_LIBRARY_LINK_CXX_FLAGS "")
endif()

project(TechTree)

if (NOT CMAKE_BUILD_TYPE STREQUAL Debug)
  set(IS_RELEASE TRUE)
endif()

include_directories(include/)

if (IS_RELEASE)
  include(CheckIPOSupported)
  check_ipo_supported(RESULT ENABLE_LTO OUTPUT error)
  if (NOT MSVC)
    add_link_options(-s)
  endif()
endif()

add_compile_definitions(WINVER=0x0501)

if(MSVC)
  foreach(flag_var
      CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
      CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
    if(${flag_var} MATCHES "/MD")
      string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
    endif(${flag_var} MATCHES "/MD")
  endforeach(flag_var)
else()
  add_compile_options(-fno-exceptions)
  if (IS_RELEASE)
    add_link_options(-Wl,--exclude-all-symbols)
  endif()
  add_link_options(-static-libstdc++ -static-libgcc)
  add_definitions(-Wall -Wsign-compare)
endif()

set(SOURCE_FILES
  TechTree/AnotherControlRelatedClass.cpp
  TechTree/BaseUnitData.cpp
  TechTree/ButtonControl.cpp
  TechTree/CivData.cpp
  TechTree/ComboBoxControl.cpp
  TechTree/Control.cpp
  TechTree/ControlListElement.cpp
  TechTree/DirectDrawBufferData.cpp
  TechTree/DirectDrawBufferDataListElement.cpp
  TechTree/DirectDrawHandler.cpp
  TechTree/DropDownButtonControl.cpp
  TechTree/FontData.cpp
  TechTree/Game.cpp
  TechTree/GameDataHandler.cpp
  TechTree/LabelControl.cpp
  TechTree/LabelLineData.cpp
  TechTree/ListBoxControl.cpp
  TechTree/Panel.cpp
  TechTree/ResearchData.cpp
  TechTree/ResearchDataItem.cpp
  TechTree/ScrollBarControl.cpp
  TechTree/SlpFile.cpp
  TechTree/SlpFileElement.cpp
  TechTree/SlpFrameHeader.cpp
  TechTree/StaticControlContainer.cpp
  TechTree/TechTree.cpp
  TechTree/TechTreeData.cpp
  TechTree/TechTreeDesign.cpp
  TechTree/TechTreeElement.cpp
  TechTree/TechTreeRenderer.cpp
  TechTree/TechTreeWindow.cpp
  TechTree/TextBoxControl.cpp
  TechTree/TextFormatData.cpp
  TechTree/Unknown1064ByteDirectDrawRelatedClass.cpp
  TechTree/UnknownPaletteRelatedClass.cpp
  TechTree/VanillaTechTreeRenderer.cpp
  TechTree/Window.cpp
  TechTree/functions.cpp
)

add_library(TechTree SHARED ${SOURCE_FILES})
set_target_properties(TechTree PROPERTIES PREFIX "" SUFFIX ".dll")
if(ENABLE_LTO)
  message(STATUS "Building with link time optimization")
  set_property(TARGET TechTree PROPERTY INTERPROCEDURAL_OPTIMIZATION TRUE)
endif()
# target_link_libraries(TechTree gdi32)
install(TARGETS TechTree
        LIBRARY DESTINATION lib)
