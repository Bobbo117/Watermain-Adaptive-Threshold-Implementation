# Watermain Adaptive Threshhold Implementation

- This project starts with Yang's project at https://github.com/heyitsyang/Whole-House-Water-Leak-Controller .
	- Optional adaptations:
		- Buck converter is replaced by dedicated 5v usb supply wall wart for ESP8266.
  		- Standard electronics enclosure is substituted for custom 3D printed electronics enclosure.
	
- Observations
	- Plot of one 24 hour pressure profile:
  
 	![Day](media/20240108_DAY_Plot.jpg)

  	- Water pressure varies several psi during the course of a day.
  	- Water pressure spikes when water heater is active.
  	- Water pressure recovers slowly after demand if there is a pressure regulator.
  	- Water flow may not be detected by a static threshhold for slower flow rates or during periods of higher pressure.
  	  
- How small a leak can we detect without using an impeller?"
	- Water flow duration was measured under various conditions (flush, shower, laundry, dishes, etc.)
	- An adaptive threshhold was implemented to close the main valve 10 minutes after detecting water flow.
 	- Motion detected in the bathrooms or kitchen cause a 20 minute grace period (for showers, proof of consciousness, etc.).
    
  	- Leak vol = leak rate x flow detection time + 10 min xleak rate + water residue after valve closes (approx 20 fl oz = 625 ml) 
 	- 75 ml/min flow is detected in 80 seconds. Total leak = 75(80/60) + 10(75) + 625 = 1475 ml (=6 cups).
        - 40 ml/min flow is detected in 6 minutes.  Total leak = 6(40) +10(40) + 625 = 1265 ml (=5 cups).
       	- 24 ml/min flow (22 drips/10 sec) is detected in 11 minutes.  Total leak = 11(24) + 10(24) + 625 = 1129 ml (<5 cups).
  	- Wide open faucet 5.3 liter/min flow would take 10 minutes to shut down.  Leak = 10(5.3)+.625 = 54 liters (14 gal).
  	 	- Therefore use moisture sensors to close valve immediately for larger volumes.
  	    
- Static pressure test results
	- Typical nightly results range betwwen -.03 and -.09 psi at my house.
 	- Pressure decrease will be greater if the test is run soon after the water heater is activated. 
    	- A 24 ml/min leak will cause a 3.44 psi drop during the nightly static pressure test under normal conditions.
    
# Adaptive Threshhold Theory of Operation
- The adaptive threshhold is based on a "leaky peak detector".
  	- Leaky peak - If pressure (P) rises above the peak value, the calculated peak pressure is set equal P.
  		- The peak pressure stays constant for the next 10 minutes unless P exceeds it before then.
  	 	- After 10 minutes, the peak is reduced .25 psi if it doesn't go less than P.
  		- As P falls, the calculated peak pressure generally falls at a slower rate.  Hence, the term leaky peak.
  	- Threshhold - The threshhold that defines flow is set at 1 psi below the most recent calculated leaky peak pressure.
  	- Flow - When P goes below the threshhold, the 10 minute flow timer begins.
  	- Motion Sensors reset the flow timer when motion is detected.
  	 
  	- There is a similar mechanism to determine the threshhold when the flow stops and the pressure rises again.
  
  - Examples:
  	
   	- The peak and threshhold are recalculated each time the pressure increases above the prior peak.

	  
	  ![Increase](media/20240104_165739%20Home%20p%20Incr%20plot.jpg)


	- The following plot shows the pressure decreasing at a rate of .125 psi/10 min; the peak is reduced .25 psi / 10 min until the two meet.

	  ![Decrease](media/20240104_174717%20G%20p%20decr.jpg)

    	- Adaptive Threshhold operation through two demand cycles:
  
	![Adaptive2](media/AdaptiveThreshhold2.jpg)
 
## System Operation
- Based on appliance signatures, a 10 minute flow causes the valve to shut down unless the following conditions exist:
	- Motion is detected in the bathroom or kitchens, causing a 20 minute standby period.
	- The manual override switch has been actuated.
	- Detection of moisture by external sensors cause immediate valve closure.
 	- Monitor daily leakage test and graphical representation to catch other issues.

## Flow Duration Study
- Overnight (no activity) followed by shower at 5am.
	- Note visual sign of failing toilet seal, which was seen before heard:
      
![Toilet Flush](media/ToiLeak.jpg)

- This is a great visual tool to monitor the water system.  That seal is getting worse!

![Shower](media/ToiLeak2.jpg)

- Wash Machine flow and dishwasher signatures indicate max flow duration is less than 5 minutes:

![LaundrySignature](media/LaundrySignature.jpg)

- Two Flushes and a shower.
  
![Adaptive](media/AdaptiveThreshold.jpg)

## Hardware
- PIR motion sensors with temperature/humidity report to Home Assistant, causing a 20 minute standby.
- Be sure to use a 100nf decoupling capacitor across temp sensor Vcc and Gnd to prevent spurious PIR hits.

![PIR_Disassembled](media/PIRDissassembly.jpg)

![PIR_Assembled](media/PIR_Motion_Detector.jpg)

## Tips, Tricks, and Traps

- If you go for take a long shower (> 10 minutes), verify that the bathroom motion sensor sees you!  Look for the blue light.
- For showers longer than 20 minutes, don't forget to wave at the motion sensor once in a while.
- Be sure to activate the Manual Override switch in the HASS screen for the powerwash vendor or other vendors using water.

## Results

- This system has operated 4 months with no unanticipated shutoffs.  
- Periodic slow leak testing resulted in the valve closure for the most part.
- Very slow leak rates may not close the valve, especially if the pressure happens to be on the up cycle.
   	- In this case, the nightly Static Leakage Test gives an abnormally high reading.
 
## Home Assistant Screens

- These screens along with Yang's will help you navigate your way through the software:

![Increase](media/20240104_165739%20Home%20p%20Incr.jpg)

![1](media/20240108_HA_Plot_PIR.jpg)

![2](media/20240108_HA_Settings.jpg)

![3](media/20240108_HA_Settings2.jpg)

![4](media/20240108_HA_full.jpg)











