# Standalone solar systems

## Folder description

The below zipped folders have been deposited

* Microgrid.zip: Data collected from Kigeme Microgrid (02 July 2019 to 31 March 2020)
* Hall.zip: Data collected from Nyabiheke Hall (01 July 2019 to 31 March 2020)

## Folder structure

The following zipped folders contain .csv files for CPE, socket and system data.
The naming convention for files in each folder is given below

### Microgrid

* CPE data: The files are labelled as [LOCATION]_CPE_data.csv, where LOCATION suggests where the CPE is hosted. The location can be Nursery[1A,1B,1C,2A,2B, 2C], Playground or Streetlight.
* Socket data: The files are labelled as [LOCATION]_sockets_data.csv, where LOCATION suggests where the socket is hosted. The location can be Nursery[1A,1B,1C,2A,2B, 2C] or Playground.
* System data: The file is labelled as System_data.

### Hall

* CPE data: The files are labelled as Hall_CPE_[ID]_data.csv, where ID  is the unique identifier for each CPE. The values range between 1-7. 
* Socket data: The files are labelled as Hall_S_[ID]_data.csv, where ID  is the unique identifier for each socket. The values range between 1-4. 
* System data: The file is labelled as System_data.

The following zipped folders contain the original check sheets and logbooks (in .jpg/.pdf/.docx format) submitted by the field staff.
The check sheets and logbooks are categorized by month (named as [MONTH YEAR]) between July 2019 and March 2020 for when they were available.
A synopsis of check sheets and logbooks for each intervention is also attached separately.

### Hall Checksheets

Contains check sheets that were completed for the Hall.

### Hall Logbooks

Contains logbooks that were completed for the Hall.

### Microgrid Checksheets

Contains check sheets that were completed for the Micro grid.

### Microgrid Logbooks

Contains logbooks that were completed for the Micro grid.

## File structure

The file structure for CPE, sockets and system data for both folders is given below

### CPE data

* Timestamp (Africa/Kigali) - Time at which data was collected in POSIXct format in Africa/Kigali time zone.
* ID - Unique identifier for each CPE.
* LED1_P (W) - Instantaneous power reading of LED1 in Watts.
* LED2_P (W) - Instantaneous power reading of LED2 in Watts.
* LED3_P (W) - Instantaneous power reading of LED3 in Watts.

### Socket data

* Timestamp (Africa/Kigali) - Time at which data was collected in POSIXct format in Africa/Kigali time zone.
* ID - Unique identifier for each socket. 
* vRELAY1_LVL (W) - Instantaneous power draw at a socket in Watts.

### System data

* Timestamp (Africa/Kigali) -Time at which data was collected in POSIXct format in Africa/Kigali time zone.
* Battery Monitor State of Charge (\%) - State of charge of the battery. 
* Battery Monitor Discharged Energy (kWh) - Total cumulative energy discharged from the battery since commissioning in Kilo Watt-hours.
* Battery Monitor Charged Energy (kWh) - Total cumulative energy charged to the battery since commissioning in Kilo Watt-hours.
* Solar Charger Battery Power (W) - Power delivered to the battery via the solar charge controller in Watts. 
* Solar Charger PV Power (W) - Power delivered directly to the Solar Charge Controller from PV array in Watts.
* System Overview AC Consumption (W) - Power delivered from the Inverter to the light and sockets in Watts.
* System Overview Battery Power (W) - Battery power in Watts.
