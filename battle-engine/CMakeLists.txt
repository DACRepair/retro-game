cmake_minimum_required(VERSION 3.1)
project(BattleEngine C)

option(BATTLE_ENGINE_ENABLE_FAST_MATH_OPT "Enable fast math (-ffast-math)" ON)
option(BATTLE_ENGINE_ENABLE_ARCH_NATIVE_OPT "Enable optimizations for native arch (-march=native)" OFF)
option(BATTLE_ENGINE_ENABLE_ASAN "Enable Address Sanitizer" OFF)
option(BATTLE_ENGINE_ENABLE_MEMSAN "Enable Memory Sanitizer" OFF)
option(BATTLE_ENGINE_ENABLE_UBSAN "Enable Undefined Behavior Sanitizer" OFF)
option(BATTLE_ENGINE_ENABLE_ANALYZER "Enable static analyzer" OFF)

find_package(JNI REQUIRED)
include_directories(${JNI_INCLUDE_DIRS})

add_library(BattleEngine SHARED BattleEngine.c)
set_property(TARGET BattleEngine PROPERTY C_STANDARD 99)

if(CMAKE_C_COMPILER_ID MATCHES Clang OR CMAKE_COMPILER_IS_GNUCC)
  target_compile_options(BattleEngine PRIVATE -Wall -Wextra)

  if(BATTLE_ENGINE_ENABLE_FAST_MATH_OPT)
    target_compile_options(BattleEngine PRIVATE -ffast-math)
  endif()

  if(BATTLE_ENGINE_ENABLE_ARCH_NATIVE_OPT)
    target_compile_options(BattleEngine PRIVATE -march=native)
  endif()

  if(BATTLE_ENGINE_ENABLE_ASAN)
    target_compile_options(BattleEngine PRIVATE -fsanitize=address)
    set_property(TARGET BattleEngine APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=address")
  endif()

  if(BATTLE_ENGINE_ENABLE_MEMSAN)
    target_compile_options(BattleEngine PRIVATE -fsanitize=memory)
    set_property(TARGET BattleEngine APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=memory")
  endif()

  if(BATTLE_ENGINE_ENABLE_UBSAN)
    target_compile_options(BattleEngine PRIVATE -fsanitize=undefined)
    set_property(TARGET BattleEngine APPEND_STRING PROPERTY LINK_FLAGS " -fsanitize=undefined")
  endif()

  if(BATTLE_ENGINE_ENABLE_ANALYZER)
    if(CMAKE_C_COMPILER_ID MATCHES Clang)
      target_compile_options(BattleEngine PRIVATE --analyze)
    else()
      target_compile_options(BattleEngine PRIVATE -fanalyzer)
    endif()
  endif()
endif()
