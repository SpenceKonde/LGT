# Arduino Hardware Support Package for LGT8F's 

### Spence's Comments
This is cool and all, but the way they implement it is godawful. 

Correct solution is:
* Create combined header files, like a real AVR; wrap up a new compiler package to incorporate them 
	* I already have my own compiler package that I'm going to need to use anyway if Arduino official doesn't merge my PR
	* Could roll this into that one pretty easily 
	* copy the '328p stuff, rename the device spec so it gives it's own device define and hence pulls in it's header file in io.h. Easy-peasy. 
* Get some decent pinout diagrams, particularly for the packages with different numbers of pins
* Is there a wrapper around the high current I/O? If not, there should be (maybe an argument to pinMode()?)! This is a feature that people would *LOVE* because it saves them an output transistor!
* Then, the Big Kahuna - a new linker script which puts constant variables into .rodata and memory-maps them like on the tinyAVR 0-series, 1-series, and megaAVR 0-series.
* Finally, convince the people who originally made this core to host this modified compiler package so I don't have to.

Unfortunately all this stuff is a fairly low priority for me.

### Task status

- [x] PWM & Timers update
- [x] Fast_IO update
- [ ] Analog Comparator
- [ ] Differential Amplifier update 
- [ ] Computation Accelerator

### Summary
Larduino_HSP is a 3rd party hardware support package for the LGT8F core based arduino boards. The backend of the **HSP** is fork from offical arduino distribution. We have try to keep all the standard features compatible with arduino world. So feel free to resuse all of the libraries which designed for arduino platform.


Microcontroller based on LGT8F, e.g LGT8F328P has more advanced features which not covered in standard arduino implementation. so there are also many new features merged to this 3rd party package. Important update and new features as following:

* *External/Internal crystal can be selected at runtime*
* *Fast_IO interface for fast I/O speed and small code size*
* *Full configurable PWM features, including complementary and dead-band control*
* *Differential Amplifier front-end for 12bit Analog-to-Digital converter*
* *1/2 channel 8bit Digtial-to-Analog output, campatible with `analogWrite()`*
* *More standalone I/Os*

##### *Fast_IO code snippets*

```C
void setup() {
	//set D10 to output
	fastioMode(D10, OUTPUT);

	// driver D10 to low level
	fastioWrite(D10, LOW);
}

void loop() {
	// toggle D10
	fastioToggle(D10);
}
```

##### *PWM code snippets : Solo mode*

```C
void setup() {
	// usage: pwmMode(pin, pwm_mode, freq_mode)
	// PWM_MODE_SOLO: set pwm of D5/D6 to independent mode
	// PWM_FREQ_FAST: set PWM to fast mode 
	pwmMode(D5, PWM_MODE_SOLO, PWM_FREQ_FAST);

	// usage: pwmResolution(pin, res_bits)
	// set PWM resolution to 7bit
	pwmResolution(D5, 7);
	// or we can set PWM frequency directly
	// usage: pwmFrequency(pin, freq_in_hz)
	// pwmFrequency(D5, 500000);

	// usage: pwmWrite(pin, duty)
	// Note that we have set PWM resolution to 7bit
	pwmWrite(D5, 128 >> 1);
	pwmWrite(D6, 128 >> 2);
}
```

##### *PWM code snippets : Duo/Complementary mode*

```C
// dead-band settings for complementary PWM
int deadBand = 8;
int dutyMax;

void setup() {
	// usage: pwmMode(pin, pwm_mode, freq_mode)
	// PWM_MODE_DUO1: set pwm of D5/D6 to DUO1 mode (complementary)
	// PWM_FREQ_FAST: set PWM to fast mode
	// PWM_FREQ_BOOST: boost frequency by x4
	// deadBand: set dead-band cycle for PWM 
	pwmMode(D5, PWM_MODE_DUO1, PWM_FREQ_FAST|PWM_FREQ_BOOST, deadBand);

	// we can set PWM frequency directly
	// usage: pwmFrequency(pin, freq_in_hz)
	// set PWM frequency to 300KHz, return its duty resolution
	dutyMax = pwmFrequency(D5, 300000);

	// usage: pwmWrite(pin, duty)
	// Note: maximum duty is calcuated when set pwm frequency
	pwmWrite(D5, dutyMax >> 2);
	pwmWrite(D6, dutyMax >> 2);
}
```


> More detail will be documented in coming Wiki page

### Bootloader 
The Larduino HSP bootloader is based on optiboot. Source can be found inside the [bootloader directory](https://github.com/LGTMCU/Larduino_HSP/tree/master/hardware/LGT/avr/bootloaders/lgt8fx8p)

* *Bootloader upload baudrate: 57600bps*
	
### Installation:
1. Unzip [master.zip](https://github.com/LGTMCU/Larduino_HSP/archive/master.zip)
1. Copy the [**hardware**] directory to Arduino's sketchbook directory (see below to find out where it normally resides)
1. Restart Arduino, you will see new board from [**Tools**]->[**Board**] menu.

### About arduino's sketchbook directory

You can always find this directory via the **[File]->[Preferences]** dialog. But here is the default sketchbook directory for most popluar system:

| System | Path of sketchbook |
| :----- | :----------------- |
| Windows | C:\Users\\<Username\>\Documents\Arduino |
| Mac OSX | /Users/user/Documents/Arduino |
| LINUX | /home/<Username\>/sketchbook |

### Arduino Board based on LGT8F's
Here we list one remarkable arduino board based on the LGT8F, which is designed by **[OCROBOT](http://www.ocrobot.com/doku.php?id=start)**, the [ALPHA 8F382P-U](http://www.ocrobot.com/doku.php?id=ocrobot:alpha:8f328p-u:main) stick with USB to UART on-board. Very affordable, for a low cost of just **RMB 8.00**!

![](http://www.ocrobot.com/lib/exe/fetch.php?w=400&tok=f15324&media=ocrobot:alpha:8f328p-u:328p-u%E4%BE%A7%E9%9D%A2435.png)

Please follow **[OCROBOT ALPHA 8F832P-U](http://www.ocrobot.com/doku.php?id=ocrobot:alpha:8f328p-u:main)** official page for more details.
