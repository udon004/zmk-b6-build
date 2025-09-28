cmake_minimum_required(VERSION 3.13.1)

# ZMKの拡張モジュールパス（必要に応じて調整）
set(ZEPHYR_EXTRA_MODULES "${ZMK_EXTRA_MODULES};${CMAKE_CURRENT_SOURCE_DIR}/module;${CMAKE_CURRENT_SOURCE_DIR}/keymap-module")

# Zephyrのビルドシステムを読み込み
find_package(Zephyr REQUIRED HINTS ../zephyr)
project(zmk)

# 必要なリンカースクリプト（任意）
zephyr_linker_sources(SECTIONS include/linker/zmk-behaviors.ld)
zephyr_linker_sources(RODATA include/linker/zmk-events.ld)

# behavior local ID を使う場合のみ
if(CONFIG_ZMK_BEHAVIOR_LOCAL_IDS)
  zephyr_linker_sources(DATA_SECTIONS include/linker/zmk-behavior-local-id-map.ld)
endif()

# syscall ヘッダー（必要なら残す）
zephyr_syscall_header(${APPLICATION_SOURCE_DIR}/include/drivers/behavior.h)
zephyr_syscall_header(${APPLICATION_SOURCE_DIR}/include/drivers/input_processor.h)
zephyr_syscall_header(${APPLICATION_SOURCE_DIR}/include/drivers/ext_power.h)

# インクルードディレクトリ（必要なら残す）
target_include_directories(app PRIVATE include)

# 最小構成ではソースファイル不要
# ただし src/main.c を使う場合は以下を有効化
# target_sources(app PRIVATE src/main.c)

# fatal errorを有効化（任意）
zephyr_cc_option(-Wfatal-errors)
target_sources(app PRIVATE src/main.c)
