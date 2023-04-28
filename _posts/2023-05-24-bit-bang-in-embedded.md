---
layout: post
title: "Bit-bang in embedded development"
date: 2023-05-24 07:00:00 -0000
categories: embedded
---

The slang term `bit bang` is as old as computer science. Simply put: bit banging is a software technique of generating electrical patterns and/or transmit data through a digital system without the use of dedicated hardware. Though written in software, it applies specifically to interacting with hardware subsystems.

## Mocking basic IC peripherals

Let's consider a scenario, where we want to drive a LED and control its brightness. The easiest way is Pulse With Modulation, where in a specific period `T` we drive an output high for time `t1 < T` and the rest of the time (`t0 = T - t1`) we drive it low. The value `t1 / T * 100%` is the **duty cycle** ranging from 0 to 100 percent.

Now, let's say that our microcontroller doesn't have a dedicated timer/counter hardware peripheral, where we would set `t1` and `T`, point to a specific output and run the module. We have to imitate that functionality. We have to `bit bang` it.

## The example

Implementing the example above, we would do something like this (no specific CPU, just example code):
```c++
#include <cpu/io.h> // supply the PORT structure abstraction
#include <cpu/delay.h> // supply the delayMs() function

#define LED_PIN (12)
#define DUTY_CYCLE (30)
#define LED_PERIOD_MS (100)

#define LED_HIGH_TIME_MS ((DUTY_CYCLE * LED_PERIOD_MS) / 100)
#define LED_LOW_TIME_MS (LED_PERIOD_MS - LED_HIGH_TIME_MS)

void cfgIO(){
	// configure the specific IO pin as output
	PORT->CFG |= (1 << LED_PIN);
}

void set(){
	// set the output HIGH
	PORT->OUTPUT |= (1 << LED_PIN);
}

void reset(){
	// set the output LOW
	PORT->OUTPUT &= ~(1 << LED_PIN);
}

void delayHigh(){
	// delay for t1
	delayMs(LED_HIGH_TIME_MS);
}

void delayLow(){
	// delay for t0
	delayMs(LED_LOW_TIME_MS);
}

int main(){
	cfgIO();

	while(1){
		set();
		delayHigh();
		reset();
		delayLow();
	}
}

```

There are some key points we have to consider:

1. The timing strictly depends on the code structure
2. The software shouldn't do anything else if the hardware effect had to be reliable
1. The CPU is 100% busy all the time

If an interrupt would be generated, the CPU cycles would be consumed in other activities ultimately delaying the process (the effective LED period would be more than `LED_PERIOD_MS` specifies).

## A *more* advanced example

The same goes for everything where timing is crucial. See the [V-USB](https://www.obdev.at/products/vusb/index.html) AVR Firmware-Only USB stack. It was written for small microcontrollers, which don't have dedicated USB PHY and MAC peripherals. While browsing through the GitHub repo notice the `*.S` files, which have low level procedures written in AVR assembly for better performance. The USB Low Speed requires a 12 MHz timing while most of those small ICs can utilize a max 16 MHz CPU clock. So there is little room (both in time and memory footprint) for actual production code.

Although bit banging introduced severe limitations into a software project, it enables achieving functionalities unreachable without it.
