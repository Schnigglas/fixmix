# Fixmix 

Make sure the following are installed:
* Python 3 (should already be installed on most Raspberry Pi)
* [pip](https://www.raspberrypi.org/documentation/linux/software/python.md)

### Enable SPI
You'll need to enable SPI for the OLED screen to work properly. Typing the following command in the terminal will bring you to a configuration menu.

```
raspi-config 
```

Then navigate to `Interfacing Options` and select `SPI`. Make sure it's turned on and reboot.

See this [article](https://www.raspberrypi.org/documentation/hardware/raspberrypi/spi/) for more help if you need it.

### I2C
Make sure i2c is also configured properly. Type

```
sudo nano /etc/modules
```

in the terminal

press `i`, then make sure to paste the following two lines in the file:

```
i2c-bcm2708
i2c-dev
```

press `esc` then `ZZ` to save and exit.

## OLED Setup

You need to install the Adafruit Python SSD1306 library with

```
sudo python -m pip install --upgrade pip setuptools wheel
sudo pip install Adafruit-SSD1306
```

## Running the Code

First, make sure to download this repository on your raspberry pi. Once you do, navigate to the downloaded folder in the terminal:

```
cd ~/path/to/directory
```

and install the dependencies

```
sudo pip install -r requirements.txt
```

You can start the bartender by running

```
sudo python bartender.py
```

### How it Works
There are two files that support the bartender.py file:

#### drinks.py
Holds all of the possible drink options. Drinks are filtered by the values in the pump configuration. If you want to add more drinks, add more entries to `drinks_list`. If you want to add more pump beverage options, add more entries to the `drink_options`.

`drinks_list` entries have the following format:

```
{
		"name": "Gin & Tonic",
		"ingredients": {
			"gin": 50,
			"tonic": 150
		}
	}
```

`name` specifies a name that will be displayed on the OLED menu. This name doesn't have to be unique, but it will help the user identify which drink has been selected. `ingredients` contains a map of beverage options that are available in `drink_options`. Each key represents a possible drink option. The value is the amount of liquid in mL. *Note: You need a higher value for carbonated beverages since some of the CO2 might come out of solution while pumping the liquid. Additionally, it is crucial to multiply the value by 1.6 to account for any potential discrepancies in the carbonation level.*

`drink_options` entries have the following format:

```
{"name": "Gin", "value": "gin"}
```

The name will be displayed on the pump configuration menu and the value will be assigned to the pump. The pump values will filter out drinks that the user can't make with the current pump configuration. 

### pump_config.json
The pump configuration persists information about pumps and the liquids that they are assigned to. An pump entry looks like this:

```
"pump_1": {
		"name": "Pump 1",
		"pin": 17, 
		"value": "gin"
	}
```

Each pump key needs to be unique. It is comprised of `name`, `pin`, and `value`. `name` is the display name shown to the user on the pump configuration menu, `pin` is the GPIO pin attached to the relay for that particular pump, and `value` is the current selected drink. `value` doesn't need to be set initially, but it will be changed once you select an option from the configuration menu.

Our bartender only has 6 pumps, but you could easily use more by adding more pump config entries.

### A Note on Cleaning
After you use the bartender, you'll want to flush out the pump tubes in order to avoid bacteria growth. There is an easy way to do this in the configuration menu. Hook all the tubes up to a water source, then navigate to `configure`->`clean` and press the select button. All pumps will turn on to flush the existing liquid from the tubes. I take the tubes out of the water source halfway through to remove all liquid from the pumps. *Note: make sure you have a glass under the funnel to catch the flushed out liquid.*


### Running at Startup
You can configure the bartender to run at startup by starting the program from the `rc.local` file. First, make sure to get the path to the repository directory by running

```
pwd
```

from the repository folder. Copy this to your clipboard.

Next, type

```
sudo nano /etc/rc.local
```

to open the rc.local file. Next, press `i` to edit. Before the last line, add the following two lines:

```
cd your/pwd/path/here
sudo python bartender.py &
```

`your/pwd/path/here` should be replaced with the path you copied above. `sudo python bartender.py &` starts the bartender program in the background. Finally, press `esc` then `ZZ` to save and exit. 

## Web Application installation

Theres a script to install FixMix automatically.
Type in the command line the following:

```
curl https://github.com/Schnigglas/fixmix/blob/main/auto-install/install.sh | bash
```

## Using The Web Application
Navigate to the IP address of your device for example (you can usually find the IP address of your device from looking at your router's configuration/status pages). The router typically assigns IPs with prefixes of 192.168.1.XXX.  Heres an example of a network, so you'll see URLs like: http://192.168.1.42  Yours may look different depending on your routers firmware/manufacturer (i.e. 10.10.0.XXX, etc.)
