This code was written by Gil Harbaum and is meant for arduinoIDE (see Hardware Requirements below).

Go here for the info below with pictures:
https://github.com/harbaum/galagino

--------------------------------------------------------------------------------------------------

Galagino - ESP32 Arcade Emulator
Play Galaga, Pac-Man, Donkey Kong, Frogger, Digdug and 1942 on the ESP32

Cabinet

Pac-Man screenshot  Galaga screencast  Donkey Kong screenshot Frogger screenshot Digdug screenshot 1942 screenshot

Pac-Man, Galaga and Donkey Kong are three of the most iconic arcade machines of all times and are listed on places #1 to #3 on the Killer List of Video Games.

Especially Pac-Man and Galaga have been remade and emulated many times. So far the cheapest and smallest system able to do a faithful emulation of the original arcade machines was the raspberry pi. But even the much cheaper ESP32 should be able to easily emulate a machine from the early ’80s, shouldn't it?

Well, things are not that easy. The Galaga arcade was driven by three Z80 CPUs, each running at 3Mhz. Additionally the arcade machine included two more CPUs, one for button and coin handling and one for audio support. And finally the hardware itself had dedicated support for simple wavetable audio, tilemap graphics and up to 64 sprites. The video resultion was 224 by 288 pixels.

The ESP32 on the other hand comes with two cores running at 240MHz. But it lacks dedicated video hardware. Emulating the various CPUs as well as the handling of audio and graphics turned out to be challenging for the ESP32.

Cheap TFT screens with a resolution of 320 by 240 pixels are avaialable in various sizes from 2.0 inch to 3.2 inch allowing for a very small and cheap galagino setup.

These small displays usually allow for SPI clock rates of up to 40MHz allowing for a max screen refresh rate of ~30Hz. This is exactly half the refresh rate of the original arcade machine. 30Hz is sufficient for a very fluid gameplay. However, with displays coping with 80MHz SPI clock, Galagino will run at full 60Hz refresh.

Youtube videos

First Galaga Emulation test
Sound and Starfield working
Galaga is finally playable
Pac-Man on Galagino
Donkey Kong on Galagino
Boot menu
Cardboard cabinet prototype
Finished plywood cabinet
John Bradnam's 3D printed case
Frogger on Galagino
Digdug on Galagino
Hardware

Original Hardware

The hardware is built around one of those cheap ESP32 development boards like the ESP32 Devkit V4 depicted in the images below. The components needed for the breadboard setup are listed below. If you plan to build the cabinet instead you might want to make sure you order the exact parts that aren't only electrically compatible but also mechanically.

ESP32 development board (e.g. Devkit V4)
A 320x240 SPI TFT screen (no touch needed)
Either a ILI9341 based screen as depicted, or
a ST7789 based screen with 320x240 pixels
An audio amplifier and speaker
e.g. a PAM8302A and a 3W speaker (as seen in the photos), or
a Keyestudio SC8002B, or similar
five push buttons, or
one push button and a five way joystick breakout (for Pac-Man and Donkey Kong)
breadboard and wires
The entire setup should be connected as depicted below. The Devkit is too wide for the breadboard leaving no space above it to connect wires. Thus the wires going to the top pin row of the Devkit are placed underneath the DevKit with the connections done as shown in the image below. See galagino/config.h for the button mappings.

Breadboard scheme

PDF

Breadboard photo

This setup with five buttons works fine for Galaga since there is no vertical movement in the game that requires a joystick. A joystick is needed for Pac-Man and Donkey Kong. The joystick based setup would then be wired in the following way:

Breadboard scheme with 5 way joystick

PDF

"Cheap Yellow Display"

The ESP32-2432S028R A.K.A. the Cheap Yellow Display is an ESP32 Board that comes with the majority of hardware required to build a Galagino device.

Cheap Yellow Display

Hardware Needed:

Cheap Yellow Display
A Nintendo Wii nunchuck and adaptor - This can be connected to the JST connector labled "CN1" with the cable included with the display
A speaker - This can be attached to the display with a 1.25mm JST connector to the connector labled "SPEAK" (or soldered)
Note: The Start button is connected to the "boot" button on the board.

To get the project working on the Cheap Yellow Display, just uncomment the #define CHEAP_YELLOW_DISPLAY_CONF line in galagino/config.h

Software

Important notice 1: You really need to use the Arduino IDE as it provides the optimization settings required for sufficient perfgormance of Galaga and Digdug by default. E.g. PlatformIO doesn't seem to enable enough optimization.

Important notice 2: I2S/DAC support seems to be broken in the ESP32 board package version 2.0.10 and up. Please use ESP32 board package 2.0.9 or older for a clean sound.

The software consists of three parts:

The Galagino specific code contained in this repository
The orignal ROM files
Galaga (Namco Rev. B ROM)
Pac-Man (Midway)
Donkey Kong (US set 1)
Frogger
Digdug
1942
A Z80 software emulation by Marat Fayzullin
Galagino uses code that is not freely available and thus not included in this repository. Preparing the firmware thus consists of a few additional steps:

The ROM files have to be placed in the roms directory, together with the ZIP file containing the Z80 emulator.
A set of python scripts is then being used to convert and patch the ROM data and emulator code and to include the resulting code into the galagino sketch directory.
The same scripts are afterwards being used to convert audio sample files into the desired format.
The ROM conversion as well as the audio sample conversion create a whole bunch of additional files in the galagino directory. Please check the README's in the romconv and samples directories for further instructions.

With all these files in place, the galagino.ino sketch can be loaded into the Arduino IDE. The Arduino IDE must have the ESP32 board support installed and the appropriate board like e.g. the ESP32 Dev Module should be selected. Finally the default core used by Arduino should be 1 (this is the default) as Galagino will use core 0 for audio and video emulation. Furthermore the FastLED library should be installed to support the LEDs in the cabinets marquee. LED support can be disabled in the configuration if no LEDs are going to be installed.

Configuration

The Galagino code can be configured through the galagino/config.h file. This is also the place where it's possible to choose between the ILI9341 controller (default) and the ST7789.

Also the pin assignment can be adjusted as well as the TFT SPI clock or the use of LEDs for the marquee.
