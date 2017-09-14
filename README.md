Instruction for building OpenOCD 0.10 with EFR32 support
============
This repository contains necessary documentation/source code for building an unofficial OpenOCD (version 0.10) with Silicon Labs' EFR32MG12P series micro controller support.

Prerequisite
--------
To build OpenOCD 0.10, you need following packages installed:
 - build-essential
 - automake
 - libusb-dev libusb-1.0-0-dev
 - libftdi-dev
 - libhidapi-dev

You also need a copy of OpenOCD 0.10 source code from [SourceForge](https://sourceforge.net/projects/openocd/).

Apply patch
-----
[efr32_patch.diff](https://github.com/ranbaowirelessguard/openocd_efr32/blob/master/efr32_patch.diff) provides necessary changes that adds support for EFR32MG12P to OpenOCD 0.10. To apply the patch, you need to first, validate the patch

	cd <openocd_source_directory>
	git apply --stat <path_to_patch>/efr32_patch.diff

A dry run is recommended before applying patch to the project

	git apply --check <path_to_patch>/efr32_patch.diff

If no error is shown above, then you can safely apply the patch

	patch -p1 <path_to_patch>/efr32_patch.diff

Configure
-----
By default OpenOCD doesn't support ST-Link debugger if built from source. You need to manual enable this feature:

	./configure --enable-stlink

You might also need feature like J-Link and other debuggers. To enable the support of those debuggers, append following parameters:

	./configure --enable-stlink --enable-jlink --enable-openjtag --enable-cmsis-dap

To change the default installation location (from ```/usr/bin``` to wherever you want), you can configure the prefix of the binary

	./configure --prefix=<absolute_install_addr> --enable-stlink --enable-jlink --enable-openjtag --enable-cmsis-dap

Build & Install
-----

	make && make install

Usage
-----
Assume you have already cloned the project (firmware or bootloader), you could then enter the project folder and start the stlink daemon

	cd <efr32_project_folder>
	<absolute_install_addr>/bin/openocd -f target/efr32/scripts/efr32_stlink.cfg

To verify you have connected to the target, you could telnet the local processor

	telnet localhost 4444

Troubleshoot
-----
If you come across with error ```LIBUSB_ERROR_ACCESS```, you can copy  ```<absolute_install_addr>/share/openocd/contrib/60-openocd.rules``` to ```/etc/udev/rules.d/```. To apply the change, you need to restart USB service or restart your Operating System.

If you see ```Error: init mode failed (unable to connect to the target)``` in the log, try toggle the debugger mode (MCU/OUT), then try again.
