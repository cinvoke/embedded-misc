Description in Brief
Small (15 mm x 15 mm) breakout board for Texas Instruments' TCA9548A eight-channel I2C multiplexer allowing eight separate I2C devices to be controlled by a single host I2C bus. The breakout board accepts VIN from 1.65 to 5.5 V, the I2C lines all have 4K7 pull-up resistors, it has an active low reset (not broken out here but accessible via the resistor solder pad). The multiplexer supports both normal (100 kHz) and fast (400 kHz) I2C protocols. The TCA9548A is 5-volt tolerant and can also be used to translate from high to low or low to high voltages.

What is it?
Texas Instruments' TCA9548 is an I2C switch with reset that allows a single microcontroller to communicate with up to 64 sensors all with the same I2C address by assigning a unique channel to each sensor slave sub-bus. The board is connected to 3V3 and GND as well as the SDA and SCL lines from the microcontroller. The slave sensors are connected to one of eight SCL/SDA slave ports on the board. The channels are selected by sending the TCA9548A its I2C address (0X70) followed by the channel number (0b00000001 - 0b10000000). The slave sensor then may be addressed as it normally would be through the open channel. Multiple channels can be open at the same time if the corresponding slaves have different I2C addresses.

If the microcontroller detects a bus conflict the TCA9548A can be reset via the reset pin. In order to keep the board size as small as possible, this pin is not broken out on this board but it is accessible through the 4K7 reset pullup resistor bottom pad on the front of the board. If this pad is grounded, the TCA9548A will be reset.

The TCA9548A has three address bits (A0, A1 and A2) that append to the TCA9548A chip's I2C address such that eight of these I2C multiplexers can, in principle, be ganged to provide unique addresses for 64 I2C slave devices. This board only breaks out two of the eight possible addresses (A0 = A1 = 0, A2 = 0 or A2 = 1) such that two of these boards can be used to communicate with up to sixteen slave sensors all with the same native I2C address. Thus the TCA9548A device on this board can have I2C address 0x70 or 0x74.

Why did you make it?
There are many situations where it is useful to deploy multiples of the same I2C sensors for a specific application. Since most I2C sensors have at most two selectable I2C addresses some means of addressing the redundant sensors must be found. One way is to connect all the sensors to I2C SCL and multiplex the I2C SDA serial line in software. This requires some programming sophistication and ties up the microcontroller with unnecessary overhead. Another way is to attach separate wires to each I2C sensor's ADO or address bit pin, and toggle HIGH only the I2C sensor to be addressed. This requires as many extra lines as sensors and can add to complexity on the host. The TCA9548A offers a simpler solution.

Imagine a robot or other complicated device with many moving parts, each of which has one or more of the same motion sensors to track and control movement. Or imagine that one microcontroller is to monitor several of the same type of temperature/humidity sensors one in every room of a house. The TCA multiplexer provides a convenient way to poll each sensor even though they all have one of only two unique I2C addresses.

What makes it special?
This is a very small board that is easily integrated into most projects where control of multiples of the same I2C sensor is desired. Because I wanted to keep the size to the absolute minimum, I was able to allow only two of these boards to be ganged. Perhaps with a little more work I could succeed in adding two more jumpers for choosing A0 and A1. More likely would be a slight increase in size to make such an addition easier. This board is a perfect complement to the wearable MPU9250 Nano boards and BNO055 nano boards recently introduced for motion tracking.

Order the board from OSH Park and make your own or buy a fully assembled and tested board from me and see how easy it is to add multiple sensors to your project.

Note on how to use in your Arduino or Teensiduino sketch--- The TCA9548A is super easy to use. Just add this function to your sketch:

void selectI2CChannels(uint8_t channels)
{
Wire.beginTransmission(TCA9548A_ADDRESS);
Wire.write(channels); 
Wire.endTransmission();
}

where TCA9548A_ADDRESS is 0x70 with A0 = A1 = A2 = 0 as the board is by default, or 0x74 when you close the solder jumper on the front of the board to make A2 = 1. Channels refers to the data flow or control register. To access Port 0, channels = 0x01, Port1, channels = 0x02, Port 2, channels = 0x04, ..., Port 7, channels = 0x80. Multiple channels can be open at the same time by "or"ing the channel numbers. For example, if you want to open Ports 3 and 6, channels = 0b01001000 = 0x08 | 0x40. As long as you don't call the selectI2CChannels function again, the channels you select will stay open and you can just read and write to your I2C devices as you normally do. If you want to sequentially poll devices all with the same I2C address, just select the first channel, read the data, select the next channel, read the data, etc. Easy Peasy!