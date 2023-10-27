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


select * from dbo.cybersecurity_attacks;


--Drop payload_Data column

    ALTER TABLE dbo.cybersecurity_attacks drop column payload_Data;

-- Drop device_information column

    alter table dbo.cybersecurity_attacks drop column device_information;

-- replace null values in Malware_indicators and Alert_warnings with No detection and Alert Triggered

    select alerts_warnings,isnull(NULL,'Alert Triggered') from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set Alerts_warnings = isnull(NULL,'Alert Triggered') from dbo.cybersecurity_attacks;


    select Malware_indicators,isnull(null,'IoC Detected') from dbo.cybersecurity_attacks

    update dbo.cybersecurity_attacks
    set Malware_Indicators = isnull(NULL,'IoC Detected') from dbo.cybersecurity_attacks;

-- create two column for geolocation

    select geo_location_data, Parsename(replace(geo_location_data,',','.'),2)as Geo_location_data,
    Parsename(replace(geo_location_data,',','.'),1) as Geo_Sub_location_data from dbo.cybersecurity_attacks;


    alter table dbo.cybersecurity_attacks
    add Geo_Sub_location_data NVARCHAR(50);

    update dbo.cybersecurity_attacks
    set Geo_Sub_location_data = Parsename(replace(geo_location_data,',','.'),1) from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set Geo_location_data = Parsename(replace(geo_location_data,',','.'),2) FROM DBO.cybersecurity_attacks;

-- Delete null values from Proxy_Information

    delete from dbo.cybersecurity_attacks where proxy_information is null;

    select proxy_information from dbo.cybersecurity_attacks;

--Replace Firewall_logs and IDS_IPS_Alerts null data with Log Data and Alert Data

    select isnull(Firewall_logs,'Log Data'),isnull(IDS_IPS_Alerts,'Alert Data') from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set Firewall_logs = isnull(Firewall_logs,'Log Data') from dbo.cybersecurity_attacks;

    update dbo.cybersecurity_attacks
    set IDS_IPS_Alerts = isnull(IDS_IPS_Alerts,'Alert Data') from dbo.cybersecurity_attacks;







