# stm32-vscode-cubemx-template

Template for programming, flashing and debugging STM32 Microcontrollers with CubeMX and VScode using Makefile.


## Toolchain ⚙️

- STM32CubeMX -> Generates startup code and Makefile
- VSCode -> Main editor
- Clangd -> Language server
- Compiledb -> Generates compile_commands.json for clangd
- OpenOCD -> Flasher
- Cortex-Debug -> Debugging in VSCode
- ARM-GCC -> Compiler and debugger


## Required tools 🔧
STM32CubeMX

VSCode with extensions
- C/C++
- Cortex-Debug
- clangd

GCC-ARM compiler and debugger
```
sudo pacman -S arm-none-eabi-gcc arm-none-eabi-binutils arm-none-eabi-newlib arm-none-eabi-gdb
```

OpenOCD Flasher
```
sudo pacman -S openocd
```

Compiledb
```
sudo pacman -S python-pipx
pipx ensurepath
source ~/.bashrc
pipx install compiledb
```


## Manual 📖
Change to your MCU ❗
- Makefile -> flash section
- launch.json -> device, svdFile (ST MCU webpage, CAD Resources tab), configFiles

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

Cleaning build
```
make clean
```