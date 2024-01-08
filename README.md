# Smart-Home-Water-Leak-Detector

- This project starts with Yang's project at https://github.com/heyitsyang/Whole-House-Water-Leak-Controller .  Optional adaptations:
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
   
 	- 75 ml/min flow is detected in 80 seconds. Total leak = 75(80/60) + 10(75) = 850 ml (3+ cups).
        - 40 ml/min flow is detected in 6 minutes.  Total leak = 6(40) +10(40) = 640 ml (<3 cups). 
       	- 24 ml/min flow (22 drips/10 sec) is detected in 11 minutes.  Total leak = 11(24) + 10(24) = 504 ml (2 cups).
  
- How to interpret nightly static leakage test results
	- Typical nightly results range betwwen -.06 and -.2 psi at my house.
 	- pressure decrease will be greater if the test is run soon after the water heater is activated. 
    	- A 24 ml/min leak will cause a 3.44 psi drop during the nightly static pressure test under normal conditions.
    
- Adaptive threshhold theory of operation
  	- Leaky peak - If pressure (P) rises above the peak value, the calculated peak pressure is set equal P.
  		- The peak pressure stays constant for the next 10 minutes unless P exceeds it before then.
  	 	- After 10 minutes, the peak is reduced .25 psi if it doesn't go less than P.
  		- As P falls, the calculated peak pressure generally falls at a slower rate.  Hence, the term leaky peak.
  	- Threshhold - The threshhold that defines flow is set at 1 psi below the most recent calculated leaky peak pressure.
  	- Flow - When P goes below the threshhold, the 10 minute flow timer begins.
  	- Motion Sensors reset the flow timer when motion is detected.
  	 
  	- There is a similar mechanism to calculate the threshhold when the flow stops and the pressure rises again.
  
  - Examples:
  	- The folllowing cellphone screenshot was taken as pressure was increasing.
   	- The peak and threshhold are recalculated each time the pressure increases above the prior peak.

	  ![Increase](media/20240104_165739%20Home%20p%20Incr.jpg)

	- The following plot shows the pressure decreasing at a rate of .125 psi/10 min; the peak is reduced .25 psi / 10 min until the two meet

	  ![Decrease](media/20240104_174717%20G%20p%20decr.jpg)
 
## System Operation
- Based on appliance signatures, a 10 minute flow causes the valve to shut down unless the following conditions exist:
	- Motion is detected in the bathroom or kitchens, causing a 20 minute standby period.
	- The manual override switch has been actuated.
	- Detection of moisture by external sensors caouse immediate valve closure.

## Adaptive Threshhold Theory of Operation
- The adaptive threshhold is based on a "leaky peak detector".
 	- It rides 1 psi below the incoming MAX psi as the incoming psi slowly ebbs and flows.
  	- A faster then normal drop in psi below the threshold initiates the 10 minute flow timer and also sets second thresshold to determine when the flow has stopped.
   	- If the flow timer reached 10 minutes and the standby timer is not active, the main valve is shut off.
   	- Motion in the kitchen or bathroom overrides the operation by restting the standby timer.
   	- Time durations of wash machine and dishwasher cycles were measured to determine timer cutoffs.




## Flow Duration Study
- Overnight activity followed by shower at 5am.
	- Note visual sign of failing toilet seal, which was seen before heard:
      
![Toilet Flush](media/ToiLeak.jpg)

- Overnight activity followed by 2 toilet flushes, shower

![Shower](media/ToiLeak2.jpg)

- Wash Machine and dishwasher flow signatures indicate max flow duration is less than 5 minutes:

![LaundrySignature](media/LaundrySignature.jpg)

- Adaptive Threshhold tracking intermittent slow flow
  
![Adaptive2](media/AdaptiveThreshhold2.jpg)

- Two Flushes and a shower.
  
![Adaptive](media/AdaptiveThreshold.jpg)

## Hardware
- PIR motion sensors with tempeerature/humidity

![PIR_Disassembled](media/PIRDissassembly.jpg)

![PIR_Assembled](media/PIR_Motion_Detector.jpg)

- Valve is instaled between silt filter and charcoal filter

## Home Assistant Screens

![1](20240108_HA_Plot_PIR.jpg)

![2](20240108_HA_Settings.jpg)

![3](20240108_HA_Settings2.jpg)

![4](20240108_HA_full.jpg)











