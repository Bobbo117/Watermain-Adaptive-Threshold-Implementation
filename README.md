# Smart-Home-Water-Leak-Detector

- This project is an adaptation of the project at https://github.com/heyitsyang/Whole-House-Water-Leak-Controller .

- Detects water leaks and shuts off the main water line to limit water damage.
	- Water damage insurance claims are frequent, expensive, inconvenient, and can result in insurance cancellation.

-  Hardware Alterations (optional)
	- Buck converter is eliminated -
 	- Dedicated 5v supply wall wart is provided for ESP8266
  	- Standard electronics enclosure is substituted for custom 3D printed electronics enclosure

 - Software Alterations
 	- Dependency on Home Assistant is secondary; Cellular iOT is primary control 
	- Pressure monitoring detects water leak based on duration of reduced pressure over adjustable defined time limit. 
   	- Standby mode disables shutoff to allow for anticipated excess water flow such as long showers by your teenager, etc.
	- Apply adaptive threshhold to detect smaller leaks than fixed threshhold detects while preventing premature watermain cutoff

 ## Adaptive Threshhold Theory of Operation
 - Incoming water pressure varies by 20 psi during the course of a day, usually building up during the night and dropping as people rise in the morning.
 - The adaptive threshhold is a "leaky peak detector".
 	- It rides 3psi below the incoming psi as the incoming psi slowly ebbs and flows.
  	- A faster then normal drop in psi below the threshold initiates a timer and also sets second thresshold above the floor to determine when to stop the timer.
   	- If the timer runs out, the main valve is shut off.
   	- Motion in the kitchen or bathroom overrides the operation by restting the timer.
   	- Time durations of wash machine and dishwasher cycles were measured to determine timer cutoffs.
   	  
