# Adding the standard blinky FreeRTOS demo for the Arduino Due target

FreeRTOS already comes with a demo for the ATSAM3X8E microprocessor but this demo obviously targets the SAM3X-EK
evaluation kit. And it features the atmel studio build system only.

However, starting with a working demo for the microprocessor, just replacing the LED pin definitions to match the
Arduino Due's board layout and adding a minimal build system seemed to be quite doable.

## What does this demo firmware actually do?

Standard [Simple Blinky Demo](https://www.freertos.org/simple-freertos-demos.html) which will toggle the amber LED on the Arduino Due board

## What has been done to create this demo?

- Vanilla [FreeRTOS v10.2.1](https://sourceforge.net/projects/freertos/) from SourceForge
- I've started with a copy of the "CORTEX_ATSAM3X_Atmel_Studio" demo included in the FreeRTOS release, according to the [Source Organization Convention](https://www.freertos.org/a00017.html)
- I've added new board definitions and the board initialization (below asf/sam/boards) that I took from a newer ASF release
- Adapted the demo to use the amber led on the Arduino Due board (Pin PB27)
- Creating a CMake based build system, using only the build tools that are available in the standard debian repositories

## Setting up the build environment

Tested and run on Debian Jessie. The tool chain for cross compiling and deploying the binary on the target can be
installed from the standard package repositories:

`apt-get install gcc-arm-none-eabi cmake bossa-cli`

## Building the firmware

Standard CMake build procedure: Create a build directory, generate the makefile in ther build dir, run make, enjoy

    mkdir  /tmp/testbuild
    cd !$
    
    cmake <path to the project directory>/Demo/CORTEX_M3_ARDUINO_DUE_GCC/
    -- The C compiler identification is GNU 4.9.2
    -- The CXX compiler identification is GNU 4.9.2
    -- Check for working C compiler: /usr/bin/cc
    -- Check for working C compiler: /usr/bin/cc -- works
    -- Detecting C compiler ABI info
    -- Detecting C compiler ABI info - done
    -- Detecting C compile features
    -- Detecting C compile features - done
    -- Check for working CXX compiler: /usr/bin/c++
    -- Check for working CXX compiler: /usr/bin/c++ -- works
    -- Detecting CXX compiler ABI info
    -- Detecting CXX compiler ABI info - done
    -- Detecting CXX compile features
    -- Detecting CXX compile features - done
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /tmp/test_build
    
    make
    ...
     94%] Linking C executable blink
    [ 94%] Built target blink
    Scanning dependencies of target run
    [100%] Generating blink.bin
    [100%] Built target run

## Deploying the firmware

- Connect the Arduino Due via USB with the build machine. I was using the left port (i.e. the port closer to the power connector) on the Arduino Due
- Press the "Erase" button on the Arduino Board for a couple of seconds
- Run the "make deploy" target from the firmware build

```
make deploy
[ 94%] Built target blink
Scanning dependencies of target deploy
Device found on ttyACM1
Unlock all regions
Erase flash
Write 25360 bytes to flash
[==============================] 100% (100/100 pages)
Verify 25360 bytes of flash
[==============================] 100% (100/100 pages)
Verify successful
Set boot flash true
Device       : ATSAM3X8
Chip ID      : 285e0a60
Version      : v1.1 Dec 15 2010 19:25:04
Address      : 524288
Pages        : 2048
Page Size    : 256 bytes
Total Size   : 512KB
Planes       : 2
Lock Regions : 32
Locked       : none
Security     : false
Boot Flash   : true
CPU reset.
[100%] Built target deploy
```

