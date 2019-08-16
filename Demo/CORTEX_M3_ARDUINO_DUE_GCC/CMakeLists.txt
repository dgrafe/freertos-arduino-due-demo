PROJECT(ArduinoDueFreeRTOSDemo)
CMAKE_MINIMUM_REQUIRED(VERSION 3.6)

SET(CMAKE_SYSTEM_NAME Generic)
SET(CMAKE_SYSTEM_PROCESSOR arm)
SET(CMAKE_CROSSCOMPILING 1)

SET(CMAKE_C_COMPILER arm-none-eabi-gcc)
SET(CMAKE_CXX_COMPILER arm-none-eabi-g++)
SET(CMAKE_C_FLAGS "-mcpu=cortex-m3 -mthumb -O0 -include FreeRTOSConfig.h" CACHE STRING "" FORCE)
SET(CMAKE_EXE_LINKER_FLAGS "-mcpu=cortex-m3 -mthumb -static -static-libgcc -nostartfiles -specs=nano.specs -T${PROJECT_SOURCE_DIR}/src/asf/sam/utils/linker_scripts/sam3x/sam3x8/gcc/flash.ld")
SET(CMAKE_EXE_LINKER_FLAGS_INIT "-static -static-libgcc")

ADD_DEFINITIONS(-D__SAM3X8E__ -DBOARD=ARDUINO_DUE_X)

INCLUDE_DIRECTORIES(../../Source/portable/GCC/ARM_CM3/
                    ../../Source/include/
                    src/
                    src/config/
                    src/asf/sam/utils/
                    src/asf/sam/utils/cmsis/sam3x/include/
                    src/asf/sam/utils/preprocessor
                    src/asf/sam/utils/header_files/
                    src/asf/sam/utils/cmsis/sam3x/source/templates/
                    src/asf/sam/drivers/pmc/
                    src/asf/sam/drivers/pio/
                    src/asf/sam/drivers/usart/
                    src/asf/thirdparty/CMSIS/Include/
                    src/asf/common/services/gpio/
                    src/asf/common/services/clock/
                    src/asf/common/utils/
                    src/asf/common/boards/
                    src/asf/sam/boards/

)


ADD_EXECUTABLE(blink 
               src/main.c
               src/main_blinky.c
               src/ParTest.c
               src/init_fix.c
               src/asf/sam/utils/cmsis/sam3x/source/templates/exceptions.c
               src/asf/sam/utils/cmsis/sam3x/source/templates/gcc/startup_sam3x.c
               src/asf/sam/utils/cmsis/sam3x/source/templates/system_sam3x.c
               src/asf/sam/drivers/pio/pio.c
               src/asf/sam/drivers/pmc/pmc.c
               src/asf/common/services/clock/sam3x/sysclk.c
               src/asf/sam/boards/arduino_due_x/init.c
               ../../Source/portable/GCC/ARM_CM3/port.c
               ../../Source/tasks.c
               ../../Source/list.c
               ../../Source/queue.c
               ../../Source/timers.c
               ../../Source/portable/MemMang/heap_1.c
               )

ADD_CUSTOM_COMMAND(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/blink.bin COMMAND arm-none-eabi-objcopy ARGS -O binary blink blink.bin DEPENDS blink)
ADD_CUSTOM_TARGET(run ALL DEPENDS blink.bin)


ADD_CUSTOM_target(deploy COMMAND bossac -i -u -e -w -b "blink.bin" -v -R DEPENDS blink.bin)