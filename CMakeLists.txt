# Generated Cmake Pico project file

cmake_minimum_required(VERSION 3.13)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Initialise pico_sdk from installed location
# (note this can come from environment, CMake cache etc)

# == DO NOT EDIT THE FOLLOWING LINES for the Raspberry Pi Pico VS Code Extension to work ==
if(WIN32)
    set(USERHOME $ENV{USERPROFILE})
else()
    set(USERHOME $ENV{HOME})
endif()
set(sdkVersion 2.1.1)
set(toolchainVersion 14_2_Rel1)
set(picotoolVersion 2.1.1)
set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)
if (EXISTS ${picoVscode})
    include(${picoVscode})
endif()
# ====================================================================================
set(PICO_BOARD pico2_w CACHE STRING "Board type")

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

project(pico2w_ble_6dof_sensor C CXX ASM)

# Initialise the Raspberry Pi Pico SDK
pico_sdk_init()

# Add executable. Default name is the project name, version 0.1

# Standalone example that reads from the on board temperature sensor and sends notifications via BLE
# Flashes slowly each second to show it's running
add_executable(pico2w_ble_6dof_sensor
    main.c 
    server_common.c
    lsm303_sensor/lsm303dlm.c
    )

pico_set_program_name(pico2w_ble_6dof_sensor "pico2w_ble_6dof_sensor")
pico_set_program_version(pico2w_ble_6dof_sensor "0.1")

# Modify the below lines to enable/disable output over UART/USB
pico_enable_stdio_uart(pico2w_ble_6dof_sensor 0)
pico_enable_stdio_usb(pico2w_ble_6dof_sensor 1)

target_link_libraries(pico2w_ble_6dof_sensor
    pico_stdlib
    pico_btstack_ble
    pico_btstack_cyw43
    pico_cyw43_arch_none
    hardware_adc
    hardware_i2c
    )
target_include_directories(pico2w_ble_6dof_sensor PRIVATE
    ${CMAKE_CURRENT_LIST_DIR} # For btstack config
    )
pico_btstack_make_gatt_header(pico2w_ble_6dof_sensor PRIVATE "${CMAKE_CURRENT_LIST_DIR}/6dof_sensor.gatt")

pico_add_extra_outputs(pico2w_ble_6dof_sensor)


