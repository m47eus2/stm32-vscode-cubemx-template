# stm32-vscode-cubemx-template

Template for programming, flashing and debugging STM32 Microcontrollers with CubeMX and VSCode using Makefile.


## Toolchain 🔗

- STM32CubeMX -> Generates startup code and Makefile
- VSCode -> Main editor
- Clangd -> Language server
- Compiledb -> Generates compile_commands.json for clangd
- OpenOCD -> Flasher
- STM32CubeProgrammerCLI -> Flasher (for new STM32 families)
- Cortex-Debug -> Debugging in VSCode
- ARM-GCC -> Compiler and debugger


# Required tools 🔧

## STM32CubeMX

**Arch**

yay -S stm32cubemx

**Fedora**

If using Fedora 43 it is possible you may need to replace CubeMX's JRE. 

During installation process make note of install location. After installing CubeMX run
```
sudo dnf install java-latest-openjdk
cd <CubeMX directory>
mv jre jre.backup
ln /usr/lib/jvm/java-latest-openjdk jre
```

**Ubuntu**

Install directly from ST website.

## VSCode with extensions
- C/C++
- Cortex-Debug
- clangd

## GCC-ARM compiler and debugger

**Arch**
```
sudo pacman -S arm-none-eabi-gcc arm-none-eabi-binutils arm-none-eabi-newlib arm-none-eabi-gdb
```

**Fedora**
```
sudo dnf install arm-none-eabi-gcc-cs arm-none-eabi-binutils-cs arm-none-eabi-newlib gdb
sudo ln -s /usr/bin/gdb /usr/bin/arm-none-eabi-gdb
```

**Ubuntu**

```
sudo apt install gcc-arm-none-eabi binutils-arm-none-eabi libnewlib-arm-none-eabi gdb-multiarch
```

## OpenOCD Flasher

**Arch**
```
sudo pacman -S openocd
```

**Fedora**
```
sudo dnf install openocd
```

## OpenOCD Flasher ST Fork (for new STM32 MCUs)

OpenOCD is fantastic software, but it does not support some of the newer STM32 MCUs. For this reason ST has created OpenOCD fork that supports all STM32 MCUs. The downsite of this solution is that you have to compile it yourself which is not that difficult. Alternatively you can use STM32CubeProgrammer CLI instead.

You can find OpenOCD ST fork on [GitHub](https://github.com/STMicroelectronics/OpenOCD)

Addiitonal building instructions can be found on [Official OpenOCD GitHub repo](https://github.com/openocd-org/openocd/)

**Ubuntu**
```
sudo apt install texinfo libjim-dev libtool libusb-1.0-0-dev
git clone https://github.com/STMicroelectronics/OpenOCD
cd OpenOCD/
./bootstrap
./configure --prefix=/opt/openocd-st CFLAGS="-Wno-error=calloc-transposed-args -Wno-error=discarded-qualifiers"
make
sudo make install
```

## STM32CubeProgrammer (when you don't want to use OpenOCD)

STM32CubeProgrammer is a decent alternative to the OpenOCD ST Fork. The downsite of this solution is that it is more difficult to use with Cortex-Debugg for debugging (I honestly don't know how to set it up).

**Ubuntu**

Install directly from ST website

Add to PATH .bashrc
```
export PATH=$PATH:$STM32_PRG_PATH
```
Add privilages to use USB
```
sudo cp ~/STMicroelectronics/STM32Cube/STM32CubeProgrammer/Drivers/rules/*.rules /etc/udev/rules.d/
```


## Compiledb

**Arch**
```
sudo pacman -S python-pipx
pipx ensurepath
source ~/.bashrc
pipx install compiledb
```

**Fedora**
```
sudo dnf install python3-pip
pip install compiledb
```

**Ubuntu**

```
sudo apt install pipx
pipx ensurepath
source ~/.bashrc
pipx install compiledb
```


# Project setup 📖

### 1. Get required tools

### 2. Generate project with STM32CubeMX with Toolchain set to Makefile

### 3. Open project in VSCode

### 4. Check if project builds
```
make
```

### 5. Generate compile_commands.json for clangd using compiledb with
```
compiledb make
```

**Ubuntu**

Link gcc-arm libraries directory in .clangd
```
CompileFlags:
  Add:
    - --target=arm-none-eabi
    - -I/usr/include/newlib
```

### 6. Add flashing rule in Makefile before EOF marker for flashing MCU with OpenOCD. Change target to your MCU

**OpenOCD**
```
flash: all
	openocd -f interface/stlink.cfg -f target/stm32l4x.cfg -c "program $(BUILD_DIR)/$(TARGET).elf verify reset exit"
```

**OpenOCD ST Fork**
```
flash: all
	/opt/openocd-st/bin/openocd -f interface/stlink.cfg -f target/stm32l4x.cfg -c "program $(BUILD_DIR)/$(TARGET).elf verify reset exit"
```

**STM32CubeProgrammer**
```
flash: all
	STM32_Programmer_CLI -c port=SWD -w $(BUILD_DIR)/$(TARGET).elf -v -rst
```

### 7. To configure debuging from VSCode create launch.json file - Debugging tab, Create a launch.json file, Cortex Debug. Change device, svdFile and configFiles to your MCU. SVD file can be download from ST MCU website in CAD Resources tab.

**OpenOCD**
```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Cortex Debug",
            "cwd": "${workspaceFolder}",
            "executable": "./build/STM32VScode.elf",
            "request": "launch",
            "type": "cortex-debug",
            "runToEntryPoint": "main",
            "servertype": "openocd",
            "device": "STM32L476RG",
            "svdFile": "./STM32L476.svd",
            "configFiles": [
            "interface/stlink.cfg",
            "target/stm32l4x.cfg"
            ]
        }
    ]
}
```

# Building and flashing project 🔨

Compiling project
```
make
```

Generating compile_commands.json file for clangd (required after regenerating code in CubeMX)
```
compiledb make
```

Compiling project and flashing MCU
```
make flash
```

Start debugging using VSCode
```
F5
```

Cleaning build
```
make clean
```
