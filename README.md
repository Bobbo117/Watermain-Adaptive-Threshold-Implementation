# Smart-Home-Water-Leak-Detector


- Water damage occurs frequently. It can be expensive and inconvenient.
	- Insurance claims frequently result in insurance cancellation or premium increase.
	- A silent leak can cause damage while you are away or even when you are home watching TV, napping, or sleeping overnight.

- This project is an adaptation of the project at https://github.com/heyitsyang/Whole-House-Water-Leak-Controller .
  	- It answers the question, "How small a leak can we detect without using an impeller, i.e., no moving parts?"
  	  
-  Hardware Alterations (optional)
	- Buck converter is eliminated 
 	- Dedicated 5v usb supply wall wart is provided for ESP8266
  	- Standard electronics enclosure is substituted for custom 3D printed electronics enclosure
	- No impeller is used.

- Software Enhancements
	- Detects water leaks as small as 1 cup per 6 minutes (42 ml/sec) using adaptive threshhld and shuts off the main water line automatically within 10 minutes to limit water damage.
 	- Cellular ioT control and monitoring is accomplished via ESPNOW in parallel with HA communication via MQTT.
	- Pressure monitoring detects water leak based on duration of reduced pressure over adjustable defined time limit. 
   	- Standby mode disables shutoff to allow for anticipated excess water flow such as long showers, powerwashing by contractors, etc.
	- Apply adaptive threshhold to detect smaller leaks than fixed threshhold detects while preventing premature watermain cutoff

## System Operation
- Based on appliance signatures, a 10 minute flow causes the valve to shut down unless the following conditions exist:
	- Motion is detected in the bathroom or kitchens, causing a 20 minute standby period.
	- The manual override switch has been actuated.

## Adaptive Threshhold Theory of Operation
- Incoming water pressure varies by several psi during the course of a day, usually building up during the night and dropping as people rise in the morning.
- The adaptive threshhold is based on a "leaky peak detector".
 	- It rides 1 psi below the incoming MAX psi as the incoming psi slowly ebbs and flows.
  	- A faster then normal drop in psi below the threshold initiates the 10 minute flow timer and also sets second thresshold to determine when the flow has stopped.
   	- If the flow timer reached 10 minutes and the standby timer is not active, the main valve is shut off.
   	- Motion in the kitchen or bathroom overrides the operation by restting the standby timer.
   	- Time durations of wash machine and dishwasher cycles were measured to determine timer cutoffs.
- Detection of moisture by external sensors caouse immediate valve closure. 

## Flow Duration Study
- Overnight activity followed by shower at 5am.
	- Note visual sign of failing toilet seal, which was seen before heard, enabling repair before failure!
   
![Toilet Flush](media/ToiLeak.jpg)

- Overnight activity followed by 2 toilet flushes, shower

![Shower](media/ToiLeak2.jpg)

- Wash Machine flow signature indicates max flow duration is less than 5 minutes:

![LaundrySignature](media/LaundrySignature.jpg)

- Adaptive Threshhold tracking slow leak
  
![Adaptive](media/AdaptiveThreshhold2.jpg)

![Adaptive](media/AdaptiveThreshhold.jpg)



