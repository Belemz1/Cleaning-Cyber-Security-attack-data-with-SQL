# Cleaning-Cyber-Security-attack-data-with-SQL

## INTRODUCTION

The Purpose of this task is to use SQL to clean Cyber Security attack data. We check the data for Accuracy, completeness, consistency, relevance, validity and uniqueness.
The data source is from [](https://www.kaggle.com/), and the aim is to clean with SQL then analyze and visualize with Microsoft Power BI.

## PROCEDURES

The SQL tool used in this analysis is Azure Data studio. The data is downloaded and imported into an already created data base called **PORTFOLIO**. On importation, it is seen that the data contains 40,000 rows of data.

The *SELECT TOP(1000)* query is ran to view the tables contained

    SELECT TOP (1000) [Timestamp]
      ,[Source_IP_Address]
      ,[Destination_IP_Address]
      ,[Source_Port]
      ,[Destination_Port]
      ,[Protocol]
      ,[Packet_Length]
      ,[Packet_Type]
      ,[Traffic_Type]
      ,[Payload_Data]
      ,[Malware_Indicators]
      ,[Anomaly_Scores]
      ,[Alerts_Warnings]
      ,[Attack_Type]
      ,[Attack_Signature]
      ,[Action_Taken]
      ,[Severity_Level]
      ,[User_Information]
      ,[Device_Information]
      ,[Network_Segment]
      ,[Geo_location_Data]
      ,[Proxy_Information]
      ,[Firewall_Logs]
      ,[IDS_IPS_Alerts]
      ,[Log_Source]
      FROM [Portfolio].[dbo].[cybersecurity_attacks]

![](1.0.png),![](1.1.png),![](1.2.png),![](1.3.png)

### REMOVING MILLISECONDS FROM TIMESTAMP COLUMN

The milliseconds on the timestamp column wont be needed in our analysis, therefore the need for it to be removed. The query below removes the milliseconds.

    select timestamp, convert(smalldatetime, [timestamp],0) as Timestamp from cybersecurity_attacks;

    alter table dbo.cybersecurity_attacks
    add TimestampNew smalldatetime;

    update dbo.cybersecurity_attacks 
    set TimestampNew = convert(smalldatetime, [timestamp],0) from cybersecurity_attacks;

![](2.0.png)

### DROP PAYLOAD_DATA COLUMN

The payload_Data column wont be needed in our analysis, so i had to drop that column with this query

    ALTER TABLE dbo.cybersecurity_attacks drop column payload_Data;

### DROP DEVICE_INFORMATION COLUMN

This column is also not needed in our analysis, hence i wrote this query to drop it

    alter table dbo.cybersecurity_attacks drop column device_information;

### REPLACE NULL VALUES IN MALWARE_INDICATORS AND ALERT-WARNINGS WITH 'IOC DETECTED' AND 'ALERT TRIGGERED'

![](3.0.png)

This query replaces and updates the tables

    select alerts_warnings,isnull(NULL,'Alert Triggered') from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set Alerts_warnings = isnull(NULL,'Alert Triggered') from dbo.cybersecurity_attacks;


    select Malware_indicators,isnull(null,'IoC Detected') from dbo.cybersecurity_attacks

    update dbo.cybersecurity_attacks
    set Malware_Indicators = isnull(NULL,'IoC Detected') from dbo.cybersecurity_attacks;

![](3.1.png)

### CREATE TWO COLUMN FROM GEO_LOCATION_DATA

    select geo_location_data, Parsename(replace(geo_location_data,',','.'),2)as Geo_location_data,
    Parsename(replace(geo_location_data,',','.'),1) as Geo_Sub_location_data from dbo.cybersecurity_attacks;


    alter table dbo.cybersecurity_attacks
    add Geo_Sub_location_data NVARCHAR(50);

    update dbo.cybersecurity_attacks
    set Geo_Sub_location_data = Parsename(replace(geo_location_data,',','.'),1) from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set Geo_location_data = Parsename(replace(geo_location_data,',','.'),2) FROM DBO.cybersecurity_attacks;

![](4.0.png)


### DELETE NULL VALUES FROM PROXY_INFORMATION

![](5.0.png)

    delete from dbo.cybersecurity_attacks where proxy_information is null;

    select proxy_information from dbo.cybersecurity_attacks;

![](5.1.png)

### REPLACE FIREWALL_LOGS AND IDS_IPS_ALERTS NULL DATA WITH 'LOG DATA' and 'ALERT DATA'

    select isnull(Firewall_logs,'Log Data'),isnull(IDS_IPS_Alerts,'Alert Data') from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set Firewall_logs = isnull(Firewall_logs,'Log Data') from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set IDS_IPS_Alerts = isnull(IDS_IPS_Alerts,'Alert Data') from dbo.cybersecurity_attacks;

![](6.0.png)

## CONCLUSION

In performing this task, the following string functions were used **PARSENAME**,**ISNULL**,**REPLACE** AND **CONVERT**. Some columns that were not relevant to the analysis and visualization in Power BI was dropped, and null values populated. It is also important to note that before you drop a column or alter a table, you have to be very sure because the process can not reversed.







