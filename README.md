# watermon

This is a sketch for a simple water presence sensor that detects whether water is in a reservoir, typically using a non-contact sensor and sends a notification to a messaging app on state change. The trigger for the notification is debounced to account for sloshing around in the vessel when water is being removed and will send a reminder after a set period of time if the water level has not returned to normal.

It has been tested to work with the [XKC-Y26-V](https://electropeak.com/learn/interfacing-xkc-y26-v-non-contact-water-liquid-level-sensor-with-arduino/), which is the recommended sensor as it can be directly powered by the ESP32, and the [XKC-Y26S-NPN](https://www.trumsense.com/products/xkc-y26-npn-water-level-detection-sensor-can-work-with-ic-board-pcb-board-and-alarm-mini-size-non-contact-liquid-level-sensor), which I found needs a 24v power source before it will start sending anything to the microcontroller.

## Setup

Before starting, make sure you have at set up your telegram bot. I recommend starting with a channel so you can add and remove people without having to touch the device. To get your bot token follow the [telegram guide](https://core.telegram.org/bots/tutorial#getting-ready) and take a note of your token. You can ignore the rest of the guide as we are using the API.

You'll also need somewhere to send messages. There are a number of guides to find the chat ID, such as [wikiHow](https://www.wikihow.com/Know-Chat-ID-on-Telegram-on-Android). The chat ID for a group will be a negative number and the chat ID for a regular chat will be positive.

Copy the sketch into your Arduino IDE interface and add real values for your WiFi network and Telegram bot token and chat ID. Also make sure that you connect the sensor to pin 5 or update the code to use whatever GPIO pin you like the best.

Information for wiring up the sensor is widely available as well as video tutorials like from [Circuit digest](https://www.youtube.com/watch?v=461eXyQAsSA).

## Booting

Once everything is ready, push the sketch to the ESP32 and see if everything works. On finding WiFi and booting successfully you should get an initial Telegram message and then you can test the sensor with a bottle of water or whatever you have to hand. The sensor typically has a screw to adjust the sensitivity so be ready to make changes if you are testing on anything other than your final container.

## Usage

Now you have tested everything works, you can recompile the code with the wifi details of the location you will be placing the sensor. When you plug it in, it should just work .... right .... does it .... should that red light be on .... yes, ok, fine, looks good.
