Three New Steam Systems: HeatExchanger:SteamToWater, LoadProfile:Plant:Steam, and DistrictHeatingSteam
================

**Dareum Nam, NREL**

 - Original Date: 12/3/2020
 - Revision Date: 1/28/2022
 

## Justification for New Feature ##

Steam heating for hot water loops is common in university campuses and cities like New York. Often, steam energy is transferred at the building to a hot water system via a heat exchanger. Currently users can make both steam and hot water systems, but cannot link them together. This forces modelers to use HW boilers/systems to approximate the steam systems, which isn’t accurate and reduces confidence in the energy model. The request for this new feature came from Bractlet. There have also been several upvotes on the new feature request from EnergyPlus Github.

## E-mail and Conference Call Conclusions ##

EnergyPlus Technicalities Call on 2/24/2021
- We only have one phase steam plant fluid modeling. There is a lot of room for improvement and basic validation of current steam plant.
- If we are going to add more complexity to remove assumptions, we need new ways to find those value; for example, how do we calculate the quality if we want to remove quality 0 & 1 assumption?
- New systems could use current assumption for now. And if CoolProp is implemented, the steam systems can be renewed with enthalpy-based system.
- 

## Overview ##

The current steam loop in EnergyPlus has five objects: steam boiler, steam pipe, steam to air coil, steam baseboard radiator, and condensate pump. The steam loop has several assumptions that help simplify loop complexity and increase usability.

Steam side of the loop operates on constant saturation pressure of steam
Water side (condensate) of the loop operates at atmospheric pressure
Steam loop is to operate at saturated conditions, no superheated condition
Steam loop is assumed to have no transportation losses by friction and heat transfer with surroundings so that it maintains the quality of steam throughout the system constant value of 0 or 1
Boiler operation is assumed to generate steam at quality equal to 1 every time and steam enters the coils at boiler outlet conditions
Steam coils are designed with steam traps, which only allow condensed steam to leave the coil; hence the steam always condenses and leaves the coil at quality of 0

These assumptions are applied to the new objects.

## Approach ##

Figure 1 describes the loop structure with steam to water heat exchanger.

![figure1](https://github.com/EnergyPlus/blob/AddThreeSteamModulesWithNTUMethod/design/FY2021/figure1.png)


## Testing/Validation/Data Sources ##

1. Standard unit tests were used to verify the new modules.
2. Test performance of the new modules with example test files.

## Input Output Reference Documentation ##


## Input Description ##

HeatExchanger:SteamToWater,
        \memo A steam to water heat exchanger designed to couple the steam loop to the water loop
   A1 , \field Name
        \required-field
        \reference-class-name validPlantEquipmentTypes
        \reference validPlantEquipmentNames
        \reference-class-name validCondenserEquipmentTypes
        \reference validCondenserEquipmentNames
        \reference-class-name validBranchEquipmentTypes
        \reference validBranchEquipmentNames
   A2 , \field Availability Schedule Name
        \note Availability schedule name for this system. Schedule value > 0 means the system is available.
        \note If this field is blank, the system is always available.
        \note default is that heat exchanger is on
        \type object-list
        \object-list ScheduleNames
   A3 , \field Steam Inlet Node Name
        \type node
        \required-field
   A4 , \field Steam Outlet Node Name
        \type node
        \required-field
   N1 , \field Steam Side Design Flow Rate
        \type real
        \required-field
        \minimum> 0.0
        \units m3/s
        \ip-units gal/min
        \autosizable
   A5 , \field Water Inlet Node Name
        \type node
        \required-field
        \note This connection is to the demand side of a loop and is the inlet to the heat exchanger
   A6 , \field Water Outlet Node Name
        \type node
        \required-field
        \note This connection is to the demand side of a loop
   N2 , \field Water Side Design Flow Rate
        \type real
        \required-field
        \minimum> 0.0
        \units m3/s
        \ip-units gal/min
        \autosizable
   N3 , \field Heat Exchanger U-Factor Times Area Value
        \type real
        \units W/K
        \minimum> 0.0
        \autosizable
        \required-field
   A7 , \field Control Type
        \type choice
        \key UncontrolledOn
        \key OperationSchemeModulated
        \key OperationSchemeOnOff
        \key HeatingSetpointModulated
        \key HeatingSetpointOnOff
        \default UncontrolledOn
   A8 , \field Heat Exchanger Setpoint Node Name
        \note Setpoint node is needed with any Control Type that is "*Setpoint*"
        \type node
   N4 , \field Minimum Temperature Difference to Activate Heat Exchanger
        \note Tolerance between control temperatures used to determine if heat exchanger should run.
        \type real
        \minimum 0.0
        \maximum 50
        \default 0.01
        \units deltaC
   A9, \field Heat Transfer Metering End Use Type
        \note This field controls end use reporting for heat transfer meters
        \type choice
        \key FreeCooling
        \key HeatRecovery
        \key HeatRejection
        \key HeatRecoveryForCooling
        \key HeatRecoveryForHeating
        \key LoopToLoop
        \default LoopToLoop
   N5 , \field Sizing Factor
        \note Multiplies the autosized flow rates for this device
        \type real
        \minimum> 0.0
        \default 1.0
   N6 , \field Operation Minimum Temperature Limit
        \note Lower limit on inlet temperatures, heat exchanger will not operate if either inlet is below this limit
        \type real
        \units C
   N7 ; \field Operation Maximum Temperature Limit
        \note Upper limit on inlet temperatures, heat exchanger will not operate if either inlet is above this limit
        \type real
        \units C

## Engineering Reference ##



## Example File and Transition Changes ##

No transition required. New example file(s) will be included.

## References ##

