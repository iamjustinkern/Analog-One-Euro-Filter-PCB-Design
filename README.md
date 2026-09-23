# Analog-One-Euro-Filter-PCB-Design
The circuit schematic models a Velocity Tracking - Voltage Controlled Filter. Essentially, it is an analog implementation of an One-Euro Filter.
This circuit solves the issue of noise you get when you slowly turn a knob from a low-pass soothing filter. As well as this, it will adaptively give you zero delay when you do change the parameters quickly as a heavy soothing filter typically involves some lag. Outside of the control signal soothing, it has this same effect with an input audio signal. For more sustained soft notes, the filter bandwidth would remain smaller and suppress any background hissing noise. For sharp transient attacks, the filter bandwidth will adaptively expand to maintain the characteristic of a bright sharp sound. 

Included in the repo are the KiCad files needed to print your own version of this circuit. As well as LTspice simulations as a proof of concept.

There are four major stages in this schematic that make up the architecture. 
  1. Differentiator Stage
  2. Full Wave Rectifier
  3. Alpha Control and Filter Staging
  4. LT1228 Low Pass Filtering


Differentiator Stage:

This circuit calculates the time derivative of the input signal. This is important for capturing the velocity of the input signal. In audio this corresponds with the rate of change in voltage for that signal.
The main components of this circuit involve a AD713 Operational Amplifier, with a negative feedback loop. The feedback loop has a 47K resistor coupled with a 100nF capacitor in parallel with it. From the input signal it goes through a 1K resistor and 1uF capacitor. The capacitor in the feedback loop helps give a roll off to the signal and the resistors help protect against high frequency stability issues.



Full Wave Rectifier Stage:

The Full-Wave Rectifier Circuit above converts the V_vel signal into its absolute value form. Now none of the Sine waves will be below 0. Operation Amplifiers U3 and U4 serve as a way to make the “imperfect” use of the diodes more effective with its negative feedback loops. It allows for greater precision at smaller voltage levels.

The diodes themselves help serve the rectification functionality. Putting the diodes in the op-amp loop erases their normal 0.7 volt turn on requirement. Diode 2 turns on during the positive volt swings of the sine wave. It helps route the signal for the full-wave math. Diode 1 turns on when negative swings in the signal to keep the op-amp from locking up and making the switch in directions instantaneous.

The R7 resistor has a ratio of 2:1 with the R9 resistor. It gives the half-wave path double the weight than the direct one. All the other resistors help keep the signal sizes consistent and equal.



Alpha Control and Filter Staging:

At this part of this stage of the system, we integrate a way to tune the system and execute the 1-Euro math in hardware (Fc = Fc_min + Vvel). The circuit outputs a fixed DC voltage coming from V8. This exact voltage can be tuned by R11. This resistor can change the cutoff frequency that is used to sooth the signal. During high speed motion this circuit eliminates the lag involved. The rectified signal goes through R10, which is used to tune the sensitivity of how the circuit reacts to fast movement. The voltage coming from R10 is added with the baseline voltage which boosts the signal. This forces the filter's bandwidth to open wider and lets fast movements pass through without delay. 

Op-amp U5, is what drives that summing and scaling that I mentioned above.

And op-amp U6, helps invert the signal back to its original state since the last op-amp U5 inverted it originally. It also makes sure that signal remains strong before it goes into LT1228



LT1228 Low Pass Filtering:

At the LT1228 chip is where the adaptive filtering takes place. Inside the chip contains two different amplifiers. A variable current amp and an output buffer. 

The signal flow for the variable current amp is as follows. The V_control signal coming from the filter staging circuit is fed into the Iset pin. When V_control is high it pushes high current through and when it is low, it pushes very low current. This is supposed to support the adaptive filtering mechanism of the system. The current comes out of Iout at pin 1. From there it charges the capacitor C1. If it is high current, then there is no delay and passes the high frequency content. When the current is low, the capacitor charges more slowly which allows for the signal to be smooth out and rid of the high frequency noise.

We have the output buffer stage because we don’t want to mess up the capacitor when trying to get the output. So, the second amplifier reads the voltage from the capacitor and copies it to Vout to not drain the capacitor of any power.

Finally, In- creates a global feedback loop that keeps the whole system stable and ensures that the output matches the original input.




