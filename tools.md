# demu

Device emulator is a simple piece of software that emulates devices in some configurable enviornment. The idea of demu is not to have a perfect emulation of sensors, rather, to have some sort of varying data and randomized data for testing. 

For information regarding which sensors and environmental aspects it can emulate please see the [README](https://github.com/infonet-dev/tools/blob/main/device_emulator/README.md)

# stream receiver

A simple program that registers itsself with `metra` as a metric stream receiver and begins receiving metrics. It checks to ensure that the metrics can be decoded and then dumps them to the screen. Helpful to debug the system. [README](https://github.com/infonet-dev/tools/blob/main/stream_receiver/README.md)