# Specify the minimum CMake version
cmake_minimum_required(VERSION 3.18)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_SOURCE_DIR}/stm32-cmake/cmake/stm32_gcc.cmake)

project(stm32-fetch-cube C ASM)
stm32_fetch_cube(G4)
stm32_fetch_cmsis(G4)
stm32_fetch_hal(G4)

file(GLOB_RECURSE SOURCES 
    "Core/Src/*.c"
    "${CMAKE_SOURCE_DIR}/Drivers/LCD/*.c"
    "${CMAKE_SOURCE_DIR}/Drivers/UGUI/*.c"
)

set(HAL_COMP_LIST ADC CRC I2C SPI TIM UART GPIO EXTI DMA RCC FLASH PWR CORTEX)
set(CMSIS_COMP_LIST "")

list(APPEND CMSIS_COMP_LIST STM32G4)
list(APPEND HAL_COMP_LIST STM32G4)

find_package(CMSIS COMPONENTS "${CMSIS_COMP_LIST}" REQUIRED)
find_package(HAL COMPONENTS "${HAL_COMP_LIST}" REQUIRED)

add_executable(stm32-fetch-g4 ${SOURCES} Core/Inc/stm32g4xx_hal_conf.h)

target_include_directories(stm32-fetch-g4 PRIVATE
    ${CMAKE_SOURCE_DIR}/Core/Inc
    ${CMAKE_SOURCE_DIR}/Drivers/LCD
    ${CMAKE_SOURCE_DIR}/Drivers/UGUI
)
target_link_libraries(stm32-fetch-g4
    HAL::STM32::G4::ADC
    HAL::STM32::G4::CRC
    HAL::STM32::G4::I2C
    HAL::STM32::G4::SPI
    HAL::STM32::G4::TIM
    HAL::STM32::G4::UART
    HAL::STM32::G4::GPIO
    HAL::STM32::G4::EXTI
    HAL::STM32::G4::DMA
    HAL::STM32::G4::RCC
    HAL::STM32::G4::FLASH
    HAL::STM32::G4::PWR
    HAL::STM32::G4::CORTEX
    CMSIS::STM32::G431CB
    STM32::NoSys)
stm32_add_linker_script(stm32-fetch-g4 PRIVATE ${CMAKE_SOURCE_DIR}/STM32G431CBTX_FLASH.ld)
stm32_print_size_of_target(stm32-fetch-g4)