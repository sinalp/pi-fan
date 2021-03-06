# Pi-fan introduction
Pi-fan is a small server (written in C) that can control a fan on a Raspberry Pi based on CPU temperature. For now I have only tested if this works with a Raspberry Pi 4, but it should work on all Pi's with pin 12 (BC pin 18 with PWM channel 0) available. Pi-fan works by controlling a fan with a standard 3- or 4-pin PWM (25KHz) input. It uses hardware PWM and hence uses very few cpu cycles, and generally has a very small foot print. Pi-fan uses [Mike McCauley's excellent little library bcm2835](https://www.airspayce.com/mikem/bcm2835).

## Highlights
* **Very small memory and CPU foot print.** Pi-fan uses a hardware PWM channel in the Broadcom chip, and only uses a few CPU-cycles every other second to read the temperature and adjust the fan speed, and sleeps the rest of the time. And as a small pure C-program Pi-fan just sips a small amount of RAM.
* **Simple system management integration.** Pi-fan runs as a systemd service unit, and can easily be managed via systemd.
* **Configurable**. Pi-fan can be configured wrt. how soon and how fast it should ramp up the fan speed as CPU temperature increases.
* **Built-in diagnostics.** Pi-fan has a few built-in features that makes it easier to verify that things are working as they should.


# Getting your fan up to speed
In general terms you need to go through these steps to control your fan with pi-fan:
1. Choose a suitable fan with a standard 3- or 4-wire 25KHz PWM control input.
2. Interface your fan electrically to the Pi. The gpio pins on the Pi uses 3.3V, which probably needs to be adapted to 5V or more to fit match the fan.
3. Get a compiled pi-fan executable installed on your Pi and hooked into systemd.

You can find more detailed information about each of these steps via these links:
* [Electrically interfacing your fan](docs/electrical_interface.md)
* [Procuring a pi-fan executable](docs/compiling_pi-fan.md) (also see [Pre-built executable binaries](prebuilt/binaries.md))
* [Installing and configuring pi-fan and hooking into systemd](docs/systemd_install.md)
* [Diagnosing/checking your pi-fan installation](docs/diagnosing.md)


# Flexible/configurable
Pi-fan allows you to configure the parameters listed below. While there's ample opportunity to tweak around, pi-fan works very well with the default values (listed in parenthesis)
1. Core temperature sample period, i.e. the interval between measurement of the core temperature (2 seconds).
2. How many temperature samples should be used to form the running average temperature which is used to control the fan (5).
3. **MinFan**: The lowest fan level to use (fan speed will never go below this value) (20%).
4. **MinTemp**: The minimum temperature at which pi-fan starts to increase the fan speed (30 degrees Celcius).
5. **MaxTemp**: The temperature at which the fan speed should reach 100% (55 degrees Celcius).
6. The fan level to set if pi-fan is asked to stop (50%).

This figure illustrates some of these parameters:

![pi-fan regulation](images/regulation.svg "Pi-fan regulation")

So the fan speed never goes under MinFan. Once the temperature reaches MinTemp from below, pi-fan starts to ramp up the fan speed until it reaches 100% at MaxTemp. The fan ramp-up is linear.


