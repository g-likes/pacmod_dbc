# User CAN Protocol Standard 3.0

This document is the authoritative definition of the User CAN Protocol, commonly called the CAN API.  It is the interface to the PACMod System and resides on the User CAN data bus.

## Document References
This document is the authoritative defintion of the User CAN Protocol.  The following documents are supplemental to this document.  They all reside in GitHub (https://github.com/astuff/pacmod_dbc).

- as_pacmod.dbc - The file contains the CAN Message definitions in Vector proprietary DBC file format.
- vehicle_associations.xml - The file contains the availability of each message on each of the different vehicle platforms.
- dbc_description.html - This file is derived from as_pacmod.dbc and vehicle_associations.xml.  It is a human readable version of the same file.

## Definitions

- Amber Warning Lamp: Warning lamp that indicates faults that do not require the vehicle to stop immediately.
- Red Warning Lamp: Warning lamp that indicates faults that require the vehicle to stop immediately.
- CAN ID: A CAN ID is the value in the CAN arbitration field that identifies the data in the CAN data field.
- CAN Message: A CAN message is a CAN frame with a specific value in its CAN arbitration field.
- Component: An individual electronic part of the PACMod System that communicates on the User CAN.
- DBC: The as_pacmod.dbc file.
- PACMod System: The PACMod System is the PACMod System.
- System: A vehicle system that is under by-wire control.
- User CAN: The CAN bus that interfaces to the PACMod System.
- User PC: Any device the customer uses to communicate with the PACMod System.
- Vehicle: Any vehicle under the control of the PACMod System.
- Vehicle Platform: A particular manufacturer make and model compatible with the PACMod System.

## User CAN
User CAN is the CAN bus interface to the PACMod System. It refers to both the User CAN bus and the User CAN protocol that describes the messages on the CAN bus.

## Restrictions
User CAN has the following restrictions:
1. The components on the bus are limited to the PACMod components and the customer components that need to interface to the PACMod System.
2. Messaging is limited to the defined CAN messages of the User CAN protocol.
3. Messaging beyond the interface to the PACMod system is prohibited.

## Types of CAN Messages
Each stock vehicle system under by-wire control has an associated by-wire system command message and a by-wire system report message.  A command message is a CAN message from the User PC to the PACMod System commanding the by-wire system to perform an action.  A command message is identified by a "CMD" in its name.  A report message from the PACMod System provides feedback to the User PC.  A report message is indicated by a "RPT" in its name.

Each system command and report message pair is of one of the following types:
1. Boolean: The data type carried by the command or report message is a boolean and is 8 bits long.
1. Enumeration: The data type carried by the command or report message is an enumeration and is 8 or 16 bits long.
1. Multi Enumeration: The data type carried by the command or report message is a set of enumerations.  The enumerations are of varying number and sizes limited by how they fit into 8 or 16 bits.
1. Float: The data type carried by the command or report message is 8 or 16 bit integer.

A single command and report message pair communicates data related to the by-wire control established at the corresponding operator control for a single stock vehicle parameter. Each report message includes the following fields:

1. MANUAL_INPUT - measurement from operator control for monitoring operator intent.
1. COMMANDED_VALUE - copy of the command received for diagnostic of User CAN.
1. OUTPUT_VALUE - feedback related to the PACMod System output for diagnostic of the PACMod System.

The availability of feedback data for the OUTPUT_VALUE varies between stock vehicle platforms and their unique by-wire implementations. The following list provides an order of precedence for the data that shall be written to the OUTPUT_VALUE field. Number 1 in the list is highest precedence.

1. Stock ECU data for the operator control in both manual and by-wire modes.
1. Position data from the operator control actuator in by-wire mode and equal to MANUAL_INPUT in manual mode.
1. COMMANDED_VALUE in by-wire mode and MANUAL_INPUT in manual mode.
1. Zero in both modes.

The global messages affect the PACMod System as a whole (all systems and/or components).  It is made up of the global command and the global report.  The global command affects the PACMod System as a whole.  The global report reflects the status of the PACMod System as a whole.  Global messages are identified with "GLOBAL" in its name.

System messages are made up of command messages, report messages, and have a fixed association to a single vehicle system under by-wire control.  The system command message contains the command specific to its system.  The system report contains an echo of the command, the operator command to the corresponding system, as well as the vehicle response to the command (if available).  It also contains status information.  System auxiliary reports contain additional information related to its system.  Each data field in a system auxiliary report has a corresponding data field that indicates the availability of the data for vehicle.  System messages begin with the name of a system.  Auxiliary messages are identified with "AUX" in its name.

Component reports have a fixed association to a single component.  It contains data fields that indicate the type of its component, the systems available on its component, and the status of its component.

There are other miscellaneous commands and reports.

## Signal Status Enumerations
These enumerations indicate the status of signals. They are:

ERROR - The signal is not correct.
NOT_AVAIL - The signal is not available.
All signals that can possess one or more of these statuses shall reserve the largest positive value as follows.

(N = the largest positive value for the given value space. For non-integers, consider the value space as an integer.)

## Single Bit Data
Reserve 2-bits for data and statuses, defined as follows:
0=FALSE_STATE, 1=TRUE_STATE, 2=ERROR, 3=NOT_AVAIL

## 2-7 Bit Data
Reserve two largest positive values for statuses, defined as follows.
N-1=ERROR, N=NOT_AVAIL

## 8 Bit Data and Larger
Reserve five largest positive values for statuses, defined as follows.
N-5, N-4, and N-3=RESERVED, N-1=ERROR, N=NOT_AVAIL
RESERVED values are for later use.

If one of the enumeration values do not apply to a given signal - NOT_USED may be used.

## CAN Bus
User CAN Protocol datalink layer is CAN 2.0 at 500kbps.

## CAN IDs
The list below constrains the assignment of CAN IDs to specific messages. Its purpose is to give increasing priority to the increasing time-criticality of associated data.

1. 0x000-0x03F (0-63) - System-wide reports (global and component reports meant to be received by all components)
1. 0x040-0x07F (64-127) - Safety function reports (independent of by-wire functions)
1. 0x080-0x0BF (128-191)- System-wide commands (global commands)
1. 0x0C0-0x0FF (192-255) - Safety function commands (independent of by-wire functions)
1. 0x100-0x1FF (256-511) - System-based commands
1. 0x200-0x2FF (512-767) - System-based reports
1. 0x300-0x3FF (768-1023) - System-based auxiliary reports
1. 0x400-0x4FF (1024-1279) - Misc. reports, non-time-critical commands
1. 0x500-0x5FF (1280-1535) - Internal diagnostic messages (generally undocumented)
1. 0x600-0x6BF (1536-1726) - Internal development
1. 0x6C0-0x6FF (1727-1791) - Internal use
1. 0x700-0x7FF (1792-2047) - Unused

## Rules for Transmitting CAN Messages
All system messages transmit at 30Hz.  Other messages transmit at 30Hz or less. Transmission of successive messages shall not be back-to-back but must have a minimum separation of 500 microseconds.  See the figure below.  Faster data transmission may prevent PACMod from processing all data and result in inconsistent behavior.

The figure below is an example of the minimum separation between the transmission of the BRAKE_CMD, STEERING_CMD, and the ACCEL_CMD messages by the User PC.  The User PC transmits the complete set of messages, each separated by 500us.  The user then transmits the same set of messages again on 30Hz cycle (33.3ms) later.  Each message is again separated by 500us.

![alt_text](min_xsmn_separation.jpg "min_xsmn_separation.jpg")

## CAN Signals

The byte order of all CAN signals is Motorola/Big-Endian. All negative numbers are represented as two’s complement.

## CAN Message Availability

Each vehicle has a subset of available CAN messages.  The file vehicle_associations.xml specifies the availability of each message.

## Enablement Rules

A PACMod System can be in either an enabled or disabled state. When it is enabled, the PACMod System has control over the vehicle subsystem. When the PACMod System is disabled, the operator controls have control over the vehicle subsystem.

## Methods of Enabling PACMod Systems

There are two methods to enable PACMod Systems

### Individual System Enable
The User PC can enable and disable individual PACMod Systems.
  - Enable - All of the following must be true to enable individual PACMod Systems.
    - The respective system command message is not timed out.
    - The ENABLE signal must be set to ENABLE after first being set to DISABLE.
    - If required, the Global Gommand Sanity Check Rules must be satisfied.
    - The GLOBAL_RPT_2::SYSTEM_READY signal is READY.
  - Disable - If any of the following are true, a PACMod System will disable.
    - The respective system command message times out.
    - The ENABLE signal is set to DISABLE.
    - If required, the Global Command Sanity Check Rules are not satisfied.
    - The GLOBAL_RPT_2::DISABLE_ALL_SYSTEMS signal is TRUE.
    - The GLOBAL_RPT_2::SYSTEM_READY signal is FALSE.

*Note - The following rules DO apply while using the Individual System Enable Method*
- Supervisory Enablement
- Global Command Sanity Check
- System Global Disable
- System Ready

### Global System Enable
The User PC selects the subsystems to enable while the PACMod System is disabled. The User PC can then enable all selected systems at the same time. While enabled, the User PC can independently enable and disable additional individual subsystems. The User PC can also disable all enabled subsystems at the same time.
  - Select - To select a subsystem to enable, the respective system command message must not be timed out and the ENABLE signal must be set to ENABLE.
  - Enable - The SAFETY_FUNC_CMD::COMMAND signal triggers all systems with their ENABLE signal set to ENABLE to transition to the enabled state. Once the SAFETY_FUNC_CMD::COMMAND signal is set to enable subsystems, the user PC can independently enable or disable subsystems by setting their corresponding ENABLE signals accordingly.
  - Disable - A subsystem shall only disable if commanded to do so by the safety system, either by changing the subsystems ENABLE command, or from the SAFETY_FUNC_CMD::COMMAND.

*Note - The following rules do NOT apply while using the Global System Enable Method*
- Supervisory Enablement
- Global Command Sanity Check
- System Global Disable

## Supervisory Enablement Rules

Supervisory enablement rules apply to a vehicle platform when the SUPERVISORY_ENABLE_REQUIRED in the GLOBAL_RPT message is REQUIRED.  This message allows the ECU that trasmits the system command messages and the ECU with overriding enable/disable authority to be separate ECUs.

A vehicle system under PACMod control “shall enable” if all of the following are true:
1. The ENABLE field is received as ALLOW ENABLE.
1. The respective system command message ENABLE field is received as “1” within the allowed time period.
1. The rules for the COUNTER and COMPLEMENT fields in this message are met.
1. The ENABLE field must be equal to ALLOW ENABLE and received in its corresponding message before receiving the system command message with the ENABLE field equal to “1”.

The allowed time period is 3 times the message period of this message. The same vehicle system shall disable immediately if any of the following are true:

1. This ENABLE field is DISABLE ALL SYSTEMS.
1. The respective system command message ENABLE field is “0”
1. The allowed time period has elapsed before a this message is received.
1. The rules for the COUNTER and COMPLEMENT fields in this message not are met.

## Global Command Sanity Check Rules
Global sanity check rules apply to a vehicle platform when the SANITY_CHECK_REQUIRED in the GLOBAL_COMMAND message is REQUIRED. The PACMod system shall be disabled according to the rules.

1. The master component shall validate the COUNTER and COMPLEMENT in the GLOBAL_CMD message.
1. The master component shall respond to a invalid COUNTER and COMPLEMENT only when the PACMod System is ENABLED.
1. The master component shall set DISABLE_ALL_SYSTEMS to TRUE at initialization.
1. If all PACMod components have completed initialization activities and have commenced periodic activities:
    1. If SANITY_CHECK_REQUIRED = NOT_REQUIRED:
        1. Then DISABLE_ALL_SYSTEMS = FALSE.
    1. Else if SANITY_CHECK_REQUIRED = REQUIRED:
        1. If DISABLE_ALL_SYSTEMS is TRUE, the master component shall set DISABLE_ALL_SYSTEMS to FALSE when all of the following are true:
            1. The master component receives 3 consecutive GLOBAL_CMD messages with valid COUNTER and COMPLEMENT.
            1. The GLOBAL_CMD message is not timed out.
            1. The SYSTEM_READY signal in the GLOBAL_RPT_2 message is READY.
        1. Afterward, DISABLE_ALL_SYSTEMS shall be set to TRUE when any of the following are true:
            1. Invalid COUNTER and COMPLEMENT.
            1. Timeout of the GLOBAL_CMD message.
        1. If any system is then enabled while DISABLE_ALL_SYSTEMS is FALSE, a transition to TRUE shall have the following result:
            1. The SYSTEM_FAULT_ACTIVE signal of GLOBAL_RPT_2 shall be TRUE.
            1. The Watchdog Report 2 GLOBAL_CMD_SANITY_FAULT shall be TRUE if the COUNTER and COMPLEMENT are invalid.
            1. The Watchdog Report 2 GLOBAL_CMD_TIMEOUT shall be TRUE if there is a Timeout of the GLOBAL_CMD message.
1. The User PC shall monitor the COMPONENT_RPT from the master component and register a fault when any of the following are true:
    1. Invalid COUNTER and COMPLEMENT.
    1. Timeout of the COMPONENT_RPT message.

## System Global Disable Rules
A vehicle system under PACMod control “shall enable” if all of the following are true:

1. The respective system command message ENABLE field is received as “ENABLE” within the allowed time period.
1. The rules for the COUNTER and COMPLEMENT fields in this message are met.
1. The DISABLE_ALL_SYSTEMS field must be equal to FALSE and received in its corresponding message before receiving the system command message with the ENABLE field equal to “ENABLE”.

The allowed time period is 3 times the message period of this message. The same vehicle system shall disable immediately if any of the following are true:

1. This DISABLE_ALL_SYSTEMS field is TRUE.
1. The respective system command message ENABLE field is “DISABLE”
1. The allowed time period has elapsed before this message is received.
1. The rules for the COUNTER and COMPLEMENT fields in this message not are met.

## System Ready Rules
The SYSTEM_READY signal in the GLOBAL_RPT_2 message shall be READY when all the following are true:

1. Estop is released.
1. No active faults.
1. All COMPONENT_READY signals in the COMPONENT_RPT messages from all installed components are READY.
1. The SYSTEM_OVERRIDE_ACTIVE signal in the GLOBAL_RPT_2 is NOT_OVERRIDDEN.

Otherwise the SYSTEM_READY signal in the GLOBAL_RPT_2 message shall be NOT_READY.

## User Notification Command Priorities
The buzzer and LED controls have a priority list as follows:
BUZZER_MUTE (highest), BUZZER_ON, and internal PACMod (lowest) are the buzzer control priorities.
LIGHT_COMMAND::OFF (highest), LED_BRIGHTNESS, INTERIOR_LIGHTS_RPT::AMBIENT_LIGHT_SENSOR, INTERIOR_LIGHTS_RPT::DIM_LEVEL, MAX_BRIGHTNESS (lowest) are the light control priorities, as available.

## Default Bit Values

All undefined bits shall be set to 0.

## Fixed-Body Reference Frame
Any signals that refer to the local kinematics of a vehicle shall use the following local vehicle fixed-body reference frame.

![alt_text](reference_frame.png "reference_frame.png")

# Vector DBC Rules

These rules maintain consistency and readability of as_pacmod.dbc file.

## General name rules

All names shall be 32 characters or less in length.

## Signal Name Rules

Bool signal type names shall be suffixed with a direct reference to their affirmative value.

Enumeration signal type names that require additional specificity shall be suffixed with a reference to their corresponding physical signal type. When the signal represents logical information instead of physical, one of the following suffixes shall be used:

1. MODE -  Represents a signal that is read/write and indicates differing behaviors with a given set of hardware or I/O.
1. STATE - Represents a signal that is read/write and indicates a physical or logical condition.
1. STATUS - Represents a signal that is read only and indicates a physical or logical condition.

## Signal Value Name Rules

1. All signal value names shall use capital letters.
1. All signal value names shall use the underscore character.
1. All signal value names shall use the "/" character to separate alternate meanings.

# Abbreviations

Abbreviations are only required when the name is over 32 characters.

- ACC: Adaptive Cruise Control
- ACCEL: Accelerator or Acceleration
- ACPTD: Accepted
- AUTO: Automatic
- AUTOMS: Autonomous
- AUTOMSMAN: Autonomous Manual
- AUX: Auxiliary Information
- AVAIL: Available
- BRK: Brake
- CALIB: Calibration
- CMD: Command
- CNCL: Cancel
- CTR: Center
- CONFIG: CONFIG.TXT
- CTRL: Control
- DEC: Decrease
- DECEL: Deceleration system
- DIR: Direction
- DRVR: Driver
- EBI: Endurance Brake Integration
- ENG: Engine
- EXT: External
- EXTCTRL: External Control
- ESTOP: Emergency Stop
- FUNC: Function
- INC: Increase
- INT: Internal
- INTGN: Integration
- LAT: Lattitude
- LON: Longitude
- MAN: manual
- MAX: Maximum
- MC: Master Cylinder
- MFG: Manufacturing
- NTWRK: Network
- OPCTRL: Operator Control
- PASS: Passenger
- PREV: Previous
- PRI: Primary
- PRK: Parking
- RES: Resolution
- RPM: Revolutions per minute
- RPT: Report
- RX: Received
- SEC: Secondary
- SPD: Speed
- STEER: Steering System
- STR: Steering
- SYS: System
- TEMP: Temperature
- TRC: Traction Control
- TRQ: Torque
- USR: User
- VEH: Vehicle
- VEL: Velocity
- VOL: Volume
- XBR: External Braking Request
- XMSN: Transmission
