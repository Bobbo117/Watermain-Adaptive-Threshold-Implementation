# Smart-Home-Water-Leak-Detector

 Detects water leaks and shuts off the main water line to limit water damage.

 This project is an adaptation of the project at https://github.com/heyitsyang/Whole-House-Water-Leak-Controller .

 Hardware Alterations

	Buck converter is eliminated 

	Dedicated 5v supply is provided for ESP8266

	Commercially available electronics enclosure is substituted for custom 3D printed electronics enclosure

 Software Alterations

	1. Dependency on Home Assistant is eliminated

	2. Pressure monitoring detects water leak based on duration of reduced pressure over adjustable defined time limit. 
           Standby mode disables shutoff to allow for anticipated excess water flow such as long showers by your teenager, etc.

        3. Apply adaptive threshhold to prevent premature watermain cutoff while detecting smaller flows than fixed threshhold
