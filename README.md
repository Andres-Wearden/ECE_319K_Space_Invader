# ECE_319K_Space_Invaders

<br />

## Project Description
This software application executes the renowned arcade game "Space Invaders" on a TM4C123GXL microcontroller, utilizing a Sitronix ST7735 LCD display.


## Buttons
The game controls are managed through the microcontroller's Port E pins, specifically PE1-PE3. These pins correspond to the main buttons responsible for starting the game (in either English or Spanish), pausing the game, and shooting at enemies. Unlike the pullup resistors of the microcontroller, the buttons in this game utilize internal pulldown resistors to ensure positive logic. This means that when a switch is not pressed, the digital signal on the pin is low (logical 0), and when pressed, it's high (logical 1).

To handle button presses, I've enabled edge-triggered interrupts on PE1-PE3. This ensures that the software responds to hardware when a pin experiences a rising edge (button press). When a button is pressed, the software interrupts and executes specific commands defined within the GPIOPortE_Handler function in the code.

For instance, if the software detects that the PE1-connected switch was pressed at the start of the game, the game will play in Spanish. If PE3's switch is pressed at the beginning menu, the game will play in English. During the game, PE3 is used to shoot bullets, and the switch on PE2 is used to pause the game. All these button-related commands are outlined in the GPIOPortE_Handler, which activates each time the software detects a rising edge on one of the enabled pins.

## Slide Potentiometer
A slidepot, short for slide potentiometer, is a variable resistor that changes its resistance based on the position of the slider. In our system, the slidepot is utilized to control the horizontal movement of the player on the screen. It has three pins: two for applying a constant voltage (in this case, 3.3V) across a resistive track within the slidepot, and the third pin, linked to the slider, captures the output voltage resulting from moving the slider along the track, altering the resistance between the slider and one of the outer pins.

The output voltage from the slidepot ranges from 0V to 3.3V. Depending on the slider's position, this voltage change corresponds to the player's horizontal position on the LCD screen. To make this information usable, the output voltage undergoes several processes to be converted into a practical number representing the player's position.

The TM4C microcontroller, equipped with 4 analog-to-digital (ADC) sequencers and 12 analog input channels, is crucial for this process. The slidepot output voltage is connected to port D pin 2, a valid analog input channel on the TM4C. The microcontroller's software initializes PD2 as an ADC under the "ADC_Init" function. The output voltage, ranging from 0V to 3.3V depending on the slider's position, is then converted into a 12-bit digital number through the internal hardware ADC. This digital number spans from 0 to 4095, representing the range of the slidepot output voltage.

The ADC resolution is 0.8 mV, meaning each increment in the digital number corresponds to a 0.8mV increase as the slider moves. After obtaining the digital number, the data is sampled through the Timer3A interrupt service routine (ISR) running at 10 Hz. This ISR interrupts the software every 0.1 seconds, continuously sampling the 12-bit digital signal for further processing.

The sampled data is then converted into a position by inputting the 12-bit number into a mathematical function relating the output voltage to the player's horizontal location on the LCD screen. In this case, the function is defined as (Position = (x*2685)/100000), where x is the digital input, and "Position" represents the player's position in terms of pixels away from the left edge of the screen. A digital input of 0 indicates the player is all the way to the left, while a digital input of 4095 indicates the player is all the way to the right of the screen. The vertical position remains constant at y = 160, ensuring the player does not move vertically on the screen.
