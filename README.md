# SCS
Implementation of Supervisory Control Systems for FSEV 2021 and PiEV 2022

Summary of what SCS does:

The objective of this module is to detect the implausibility which may occur due to the failure of a sensor or signal communication failure and signal the shutdown circuit under implausibility. Sometimes, due to the failure of one or more sensors, faulty outputs from the sensors lead to faulty accelerations which can lead to out-of-control situations and can also affect driver’s safety. Additionally, we have added a third position sensor which will decrease the chance of implausibility and inaccuracy in the values of pedal position percentages. This reduces the dependence on the values of the two sensors and makes the system more reliable. Now if any one of the sensors fails, we need not worry if either of the sensors’ output values is plausible. This also comes with another advantage that it becomes easy and more comfortable to identify, remove and repair the failed sensors as they are separately connected and wired. We have considered various exceptional cases that may arise due to the addition of a third sensor and we have also given proper solutions to them in the truth table in the VERIFICATION AND DECISION block of our model. The rest of the model is prepared in compliance with the rules framed by FSEV. The Simulink model will be converted into software code and implemented on the Supervisory Controls Module.  

The Accelerator-Brake Implausibility check works as a backup for BSPD in SDC. In an emergency, the driver may press both the Brakes and Accelerator pedal hard. If this is observed for more than half a second, the Shutdown circuit will be opened and motor power will be cut off.  Due to this vehicle will not accelerate further and the brakes will be applied as they work hydraulically. While BSPD is circuit-based, this module is software-based. The Simulink model will be converted into software code and implemented on Supervisory Controls Module. 

Like most on-road vehicles, our vehicles include Drive and Neutral modes. The motor controller and motor will respond to the APPS data that they received when TS is active.  Hence it's the D/N Mode selector module's responsibility to provide APPS percentage only when in drive mode and 0% when in neutral mode. While the driver demands to switch to drive mode, all necessary checks are performed and the driver needs to press the D/N switch and brakes simultaneously for the whole 1 second at least.  When the vehicle shifts into drive mode successfully, the D/N indicator light will turn green on the dashboard and the buzzer will play a unique sound for 2 seconds. The Simulink model will be converted into software code and implemented on Supervisory Controls Module. 
 
Inputs: 
3 Accelerator Pedal Position Sensors 
DC Current Sensor from Motor power supply 
Brake Fluid Pressure Sensor 
Tractive System Status 
D/N Switch Signal from the cockpit
 
Outputs: 
Acceleration data to Motor Controller 
APPS and Accelerator-Brakes Implausibility to Shutdown Circuit 
Signals to Light and Sound Indicators in Cockpit 
 
Software-based Modules: 
  APPS Implausibility Check 
    1. Convert voltage signals from sensors to 0-100% analogy signals 
    2. Check validity of signals: < 0% and > 100% values to be eliminated 
    3. Check inconsistency of signals: the difference between two values > 10% 
    4. Decide which sensor data to use: All 3 or 2 of the 3 
    5. Signal SDC if invalidity or inconsistency occurs for more than 100 milliseconds 
    6. Send filtered APPS data to Accelerator-Brake Implausibility Check 
    
  Accelerator-Brake Implausibility Check 
    1. Detect Implausibility if hard braking occurs (Brake Fluid Pressure > 30 bars) along with more than 5kW power being delivered to the Motors 
    2. Signal SDC if implausibility exists for more than 500 milliseconds 
    3. Change APPS value to 0% after 500 milliseconds under implausibility 
  
  D/N Mode Selector 
    1. Switch into Drive mode from Neutral when TS is active, Brakes are applied and the D/N switch is pressed simultaneously for 1 second 
    2. Switch into Neutral mode from Drive when Brakes are applied and the D/N switch is pressed simultaneously for 500 milliseconds 
    3. Switch into Neutral mode from Drive if TS goes offline at any point 
    4. Turn on a red light on the dashboard when in Neutral and green when in Drive mode 
    5. Make a buzzer sound when Drive mode is activated for 2 seconds

![image](https://user-images.githubusercontent.com/83658560/178239282-bc5894b8-f87d-402d-9606-acda2fa5f664.png)
