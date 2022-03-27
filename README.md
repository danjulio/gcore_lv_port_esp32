# ESP32 LVGL demo ported to gCore

This repository contains the LVGL v7 demo project for ESP32 with modifications to support [gCore](https://github.com/danjulio/gCore2).  It requires Espressif IDF v4.2.

![gCore LVGL demo](images/gcore.png)

These modifications include a new predefined board type for gCore that slightly changes the ILI9488 TFT and FT6X06 touch controller configuration.

1. Modifies ILI9488 initialization for 16-bit pixels
2. Modifies the driver to directly write out the 16-bit LVGL display buffer instead of converting it to 24-bits
3. Configures VSPI bus rate to 80 MHz
4. Adds additional setup/hold times to CS when configuring the SPI driver
5. Sets the I2C rate to 100 kHz for the touch controller (maximum speed for gCore)

Basic menuconfig setup for the demo on gCore includes the following steps (this has already been done in this repository):

1. Select the predefined board danjuliodesigns gCore in `Component config->LVGL TFT Display controller`
2. Select 240 MHz ESP32 clock and Rev 3 chip type in `Component config->ESP32-specific`
3. Deselect Slide demo widgets automatically in `Component config->lv_examples configuration`
4. Select Swap 2 bytes of RGB565 color in `Component config->LVGL configuration`
5. Select Landscape Inverted display orientation in `Component config->LVGL TFT Display controller`
6. Select FT6X06 touch panel controller in `Component config->LVLG Touch controller`
7. Configure the touch panel controller to Swap X and Y coordinate and Invert X coordinate values in `Component config->LVGL Touch controller->Touchpanel Configuration`

Build the project using the command ```idf.py build```.
	
Flash it onto gCore using the command ```idf.py -p (PORT) flash``` where (PORT) is the hardware serial port connected to gCore.

Precompiled binary files in the ```precompiled``` subdirectory may be loaded without having to install the ESP32 IDF or build the project by using the Espressif Windows-based [Flash Download Tool](https://www.espressif.com/en/support/download/other-tools).  

1. Download the binary files and put them in a known location (such as a diretory in the Documents folder).
2. Download and install the utility program in a known location (such as an Applications folder in the user's home folder).
3. Connect gCore to your computer and turn it on.
4. Run the utility program by double clicking on the executible file in the installation directory.  You may have to click through some warnings about running unknown programs that Windows will display.
5. Select ESP32 to program.
6. In the programming window that is displayed, select the SPIDownload tab, 40MHz SPI SPEED, DIO SPI Mode and load the three binary files at the locations shown in the following table by clicking the `...` buttons.  Make sure to select each file for programming by checking the checkmark box to the left of the file path.
7. Set a high baud rate like 921600 baud and select the COM port that is attached to gCore.
8. Hit START to initiate programming.  You should see the tool identify the ESP32 and display information and a progress bar increase as programming occurs.  The process should take only about 10-20 seconds.
9. Press RESET on gCore to start the demo running after programming is complete.

| File | Offset |
| --- | --- |
| bootloader.bin | 0x1000 |
| partition-table.bin | 0x8000 |
| lvgl-demo.bin | 0x10000 |

![Flash Downloading Tool Setup](images/gcore_binary_programming.png)

Turn gCore off by pressing and holding the power button for more than five seconds (this demo does not implement soft power control).

# [Original Readme] LVGL project for ESP32

This is an ESP32 demo project showcasing LVGL v7 with support for several display controllers and touch controllers.
The demo application is the `lv_demo_widgets` project from the [lv_examples](https://github.com/lvgl/lv_examples) repository.

- Version of ESP-IDF required 4.2. NOTE: We're trying to make this repo backwards compatible, usage of idf.py is encouraged.
- Version of LVGL used: 7.9.
- Version of lv_examples used: 7.9.

#### Table of content
- [Get started](#get-started)
- [Use LVGL in your ESP-IDF project](#use-lvgl-in-your-esp-idf-project)
- [Use lvgl_esp32_drivers in your project](#use-lvgl_esp32_drivers-in-your-project)
- [Platformio support](#platformio-support)
- [ESP32-S2 Support](#esp32-s2-support)

Example demo for TFT displays:

![Example GUI_DEMO with ESP32 using LVGL](images/new_photo.jpg)

Monochrome support:

![Example_monochrome demo with ESP32 using LVGL](images/new_mono.jpg)

## Display and touch controllers

The display and touch (indev) controllers are now into it's own repository, you can find it [here](https://github.com/lvgl/lvgl_esp32_drivers).
To report any issue or add new display or touch (indev) drivers you can do so in the `lvgl_esp32_drivers` repo.

## Get started

### Prerequisites

- ESP-IDF Framework.

### Note

This project tries to be compatible with both the ESP-IDF v3.x and v4.0, but using v4.0 is recommended.
Instructions assume you are using the v4.x toolchain, otherwise use the make commands, e.g. instead of running `idf.py menuconfig`, run `make menuconfig`.

### Build and run the demo.

1. Clone this project by `git clone --recurse-submodules https://github.com/lvgl/lv_port_esp32.git`, this will pull this repo and its submodules.

2. Get into the created `lv_port_esp32` directory.

3. Run `idf.py menuconfig`

4. Configure LVGL in `Components config->LVGL Configuration`. For monochrome displays use the mono theme and we suggest enabling the `unscii 8` font.

5. Configure your display and/or touch controllers in `Components config->LVGL TFT Display Configuration` and `Components config->LVGL TOUCH Configuration`.

6. Store your project configuration.

7. Build the project with `idf.py build`

8. If the build don't throw any errors, flash the demo with `idf.py -p (YOUR SERIAL PORT) flash` (with `make` this is just `make flash` - in 3.x PORT is configured in `menuconfig`)

## Use LVGL in your ESP-IDF project

LVGL now includes a Kconfig file which is used to configure most of the LVGL configuration options via menuconfig, so it's not necessary to use a custom `lv_conf.h` file.

It is recommended to add LVGL as a submodule in your IDF project's git repo.

From your project's root directory:
1. Create a directory named `components` (if you don't have one already) with `mkdir -p components`.
2. Clone the lvgl repository inside the `components` directory with `git submodule add https://github.com/lvgl/lvgl.git components/lvgl`
3. Run `idf.py menuconfig`, go to `Component config` then `LVGL configuration` to configure LVGL.

## Use lvgl_esp32_drivers in your project

It is recommended to add [lvgl_esp32_drivers](https://github.com/lvgl/lvgl_esp32_drivers) as a submodule in your IDF project's git repo.

From your project's root directory:
1. Create a directory named `components` (if you don't have one already) with `mkdir -p components`.
2. Clone the lvgl_esp32_drivers repository inside the `components` directory with `git submodule add https://github.com/lvgl/lvgl_esp32_drivers.git components/lvgl_esp32_drivers`
3. Run `idf.py menuconfig`, go to `Component config` then `LVGL TFT configuration` and `LVGL TFT Display configuration` to configure lvgl_esp32_drivers.

## Platformio support

Using the [lv_platformio](https://github.com/lvgl/lv_platformio) project add the following lines to `platformio.ini` file:

```
[env:esp32]
platform = espressif32
framework = espidf
board = esp-wrover-kit
```

Change the default environment to `default_envs = esp32`.

Modify the `main.c` like this:

```c
#include "lvgl.h"

// #include "driver.h"

#include "demo.h"

int app_main(void)
{
    lv_init();

    /* Initialize your hardware. */
    
    /* hw_init(); */

    demo_create();

    /* Create the UI or start a task for it.
     * In the end, don't forget to call `lv_task_handler` in a loop. */

    /* hw_loop(); */

    return 0;
}
```

For more information see: [platformio with espidf framework compability](https://github.com/lvgl/lv_port_esp32/issues/168).

# ESP32-S2 Support

Support for ESP32-S2 variant is Work In Progress.
Smaller displays (e.g. 320x240) work fine, but larger ones need testing.

## Background

ESP32-S2 has less on-chip SRAM than its predecessor ESP32 (520kB vs. 320kB).
This causes problems with memory allocation with large LVGL display buffers as they don't fit into the on-chip memory
and external PSRAM is not accessible by DMA.

Moreover, static allocation to external PSRAM is not yet supported
(see [GitHub issue](https://github.com/espressif/esp-idf/issues/6162)).

At this momement, the buffers are dynamicaly allocated with DMA capabilty and memory allocator handles the rest.
