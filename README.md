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


## Required tools 🔧
### STM32CubeMX
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

### VSCode with extensions
- C/C++
- Cortex-Debug
- clangd

### GCC-ARM compiler and debugger

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
sudo apt install gcc-arm-none-eabi binutils-arm-none-eabi gdb-multiarch
```

### OpenOCD Flasher

**Arch**
```
sudo pacman -S openocd
```

**Fedora**
```
sudo dnf install openocd
```

### STM32CubeProgrammer (for new STM32 families)

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


### Compiledb

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


## Project setup 📖

1. Get required tools

2. Generate project with STM32CubeMX with Toolchain set to Makefile

3. Open project in VSCode

4. Check if project builds
```
make
```

5. Generate compile_commands.json for clangd using compiledb with
```
compiledb make
```

6. Add flashing rule in Makefile before EOF marker for flashing MCU with OpenOCD. Change target to your MCU

**OpenOCD**
```
flash: all
	openocd -f interface/stlink.cfg -f target/stm32l4x.cfg -c "program $(BUILD_DIR)/$(TARGET).elf verify reset exit"
```

**STM32CubeProgrammer (for new STM32 families)**
```
flash: all
	STM32_Programmer_CLI -c port=SWD -w $(BUILD_DIR)/$(TARGET).elf -v -rst
```

7. To configure debuging from VSCode create launch.json file - Debugging tab, Create a launch.json file, Cortex Debug. Change device, svdFile and configFiles to your MCU. SVD file can be download from ST MCU website in CAD Resources tab.

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

**STM32CubeProgrammer (for new STM32 families)**
```
```

## Building and flashing project 🔨

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
