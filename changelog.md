# 4.0.0
1. (Change) Move CLEAR_FAULTS to GLOBAL_CMD.
2. (Change) Break COMPONENT_RPT into 4 messages with different IDs.
3. (Change) Move counter and compliment bit positions and add system flags to Component Reports.
4. (Add) Add CONFIG_FAULT, CAN_TIMEOUT_FAULT, and ESTOP to Component Reports.
5. (Add) Add GLOBAL_CMD message.
6. (Add) Add NOTIFICATION_CMD message.
7. (Add) Add LINEAR_ACCEL_RPT and ANG_VEL_RPT messages.
8. (Add) Add COMMAND_TIMEOUT to all system reports.

# 4.1.0
1. (Add) Add Watchdog function to Component Reports.
2. (Fix) Fix the maximum value for datafields in the Component Reports.
3. (Fix) Add transmssion rate to DBC.

# 5.0.0
1. (Change) Move E-Stop from Component Report to ESTOP_RPT.
2. (Change) Change bit position of WRITE_TO_CONFIG in SHIFT_AUX_RPT.
3. (Change) Rename "is_valid" to "avail" for all auxilary reports.
4. (Change) Rename and add description of USER_INTERACTION.
5. (Add) Add Brake Deccel System.
6. (Add) Add Sprayer System.
7. (Add) Add Wiper System.
8. (Add) Add Command Limit Reports.
9. (Add) Add GLOBAL_INTERNAL_POWER_SUPPLY_FAULT to WATCHDOG_RPT.
10. (Add) Add INTERNAL_SUPPLY_VOLTAGE_FAULT to Component Reports.
11. (Add) Add Flash File Version messages.
12. (Fix) Fix sign of WHEEL_SPD_FRONT_LEFT in WHEEL_SPEED_RPT.
13. (Fix) Add previously implemented WATCHDOG_RPT message to DBC.
14. (Fix) Add transmssion rate to VIN_RPT, Software Version Report, WATCHDOG_RPT.
15. (Fix) Add fault values in _RPT messages to DBC.

# 6.0.0
1. (Change) Change Data Length Codes (DLCs) to fit data.

# 7.0.0
1. (Change) Change SHIFT_AUX_REPORT to make more efficient use of bits.
2. (Add) Add Door System.
3. (Add) Add Door System to Component Report.
4. (Add) Add AMBIENT_LIGHT_SENSOR to INTERIOR_LIGHTS_RPT.
5. (Doc Add) Move protocol details from PACMod System User Manual to user_can_protocol.md

# 7.1.0
1. (Change) Change naming from "is_valid" to "avail" for all remaining datafields.
2. (Add) Add Engine Brake System.
3. (Add) Add Marker Lamp System.
4. (Add) Add transmission gear number report.