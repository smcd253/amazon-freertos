## Cloning
This repo uses [Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) to bring in dependent components.

Note: If you download the ZIP file provided by GitHub UI, you will not get the contents of the submodules. (The ZIP file is also not a valid git repository)

To clone using HTTPS:
```
git clone https://github.com/smcd253/amazon-freertos.git --recurse-submodules
```
Using SSH:
```
git clone git@github.com:smcd253/amazon-freertos.git --recurse-submodules
```

If you have downloaded the repo without using the `--recurse-submodules` argument, you need to run:
```
git submodule update --init --recursive
```

## Getting Started - Building your own custom FreeRTOS IoT application to interface with the I3 marketplace using the STM32:

Note: This repository currently only works on Ubuntu.

1. Clone this repository.

2. Install the [ST-LINK](https://www.st.com/en/development-tools/stsw-link004.html) Utility.
    * The following steps are from [this installation guide](https://fishpepper.de/2016/09/16/installing-using-st-link-v2-to-flash-stm32-on-linux/)
    * Install the libusb library
    ```
    sudo apt-get install libusb-1.0-0-dev
    ```
    * Download, build, and install the ST-Link utility
    ```
    git clone https://github.com/texane/stlink stlink.git
    cd stlink
    make
    #install binaries:
    sudo cp build/Debug/st-* /usr/local/bin
    #install udev rules
    sudo cp etc/udev/rules.d/49-stlinkv* /etc/udev/rules.d/
    #and restart udev
    sudo udevadm control --reload
    ```

3. Install the arm compiler tools.
    ```
    sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
    sudo apt-get update
    sudo apt-get install gcc-arm-none-eabi
    ```

4. Download Microsoft Visual Studio Code through the Ubuntu Software application.
    * You can also download VS-Code [here](https://code.visualstudio.com/download).
    * **make sure the VS-Code command line tools are added to path**

5. Modify the demo you would like to build `./demos/<demo_name>/<demo_name>.c`.

6. Plug in your STM32L4.

7. Run the following commands to build the .elf executable. (From the workspace route)
```
cmake -DBOARD=stm32l475_discovery -DCMAKE_TOOLCHAIN_FILE='/media/shmcdono/Storage/FreeRTOS/AmazonFreeRTOS/tools/cmake/toolchains/arm-gcc.cmake'  -S . -B build
cd build
make
```
8. Flash the device with your program.
```
arm-none-eabi-objcopy -O binary aws_demos.elf aws_demos.bin
st-flash write aws_demos.bin 0x8000000
```
## Debugging your Program

1. Add the Cortex-Debug VS-Code Extension through the extension browser.

2. Install gdb-arm-non-eabi using the ARM Toolchain (no idea why this isn't included in the gcc arm package).
    * Go to the [arm website](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) to download the toolchain for any operating system.
    *[Here](https://developer.arm.com/-/media/Files/downloads/gnu-rm/9-2019q4/RC2.1/gcc-arm-none-eabi-9-2019-q4-major-x86_64-linux.tar.bz2?revision=6e63531f-8cb1-40b9-bbfc-8a57cdfc01b4&la=en&hash=F761343D43A0587E8AC0925B723C04DBFB848339) is the link address for the Linux x86_64 tarball.
    * Move the file to your desired location (I like to keep it in /home/user/) and extract using the following command:
    ```
    tar xjf gcc-arm-none-eabi-9-2019-q4-major-x86_64-linux.tar.bz2
    ```
    * Delete the tar file if you are limited in storage.

3. Open launch.json in VS-Code and add the following configuration:
    ```
    {
        "cwd": "${workspaceRoot}",
        "armToolchainPath": "/home/user/gcc-arm-none-eabi-9-2019-q4-major/bin/",
        "executable": "./cmake/build/aws_demos.elf",
        "name": "Cortex Debug (ST Util)",
        "request": "launch",
        "type": "cortex-debug",
        "servertype": "stutil",
        "device": "STM32L475VG",
        "v1": false
    },
    ```

4. Build the debuggable executable.
```
cmake -DBOARD=stm32l475_discovery -DCMAKE_TOOLCHAIN_FILE='/media/shmcdono/Storage/FreeRTOS/AmazonFreeRTOS/tools/cmake/toolchains/arm-gcc.cmake' -DAFR_TOOLCHAIN_PATH='/home/shmcdono/gcc-arm-none-eabi-9-2019-q4-major/bin/' -DCMAKE_BUILD_TYPE=debug -S . -B build
```

5. Run the debugger throught the VS-Code debugging window.

For more information on Amazon FreeRTOS, refer to the [Getting Started section of Amazon FreeRTOS webpage](https://aws.amazon.com/freertos).

To directly access the **Getting Started Guide** for supported hardware platforms, click the corresponding link in the Supported Hardware section below.

For detailed documentation on Amazon FreeRTOS, refer to the [Amazon FreeRTOS User Guide](https://aws.amazon.com/documentation/freertos).

## Supported Hardware

For additional boards that are supported for Amazon FreeRTOS, please visit the [AWS Device Catalog](https://devices.amazonaws.com/search?kw=freertos)

Though FreeRTOS supports many other boards, this repository will focus on the STM32L4 Discovery Kit IoT Node. Below you will find the instructions to get started with FreeRTOS on the STM32L4. However, the steps provided in this readme are complete and will suffice:
* **STMicroelectronics** - [STM32L4 Discovery kit IoT node](http://www.st.com/en/evaluation-tools/b-l475e-iot01a.html).
    * [Getting Started Guide](https://docs.aws.amazon.com/freertos/latest/userguide/getting_started_st.html)
    * IDE: [STM32 System Workbench](http://openstm32.org/HomePage)



## amazon-freeRTOS/projects
The ```./projects``` folder contains the IDE test and demo projects for each vendor and their boards. The majority of boards can be built with both IDE and cmake (there are some exceptions!). Please refer to the Getting Started Guides above for board specific instructions.
