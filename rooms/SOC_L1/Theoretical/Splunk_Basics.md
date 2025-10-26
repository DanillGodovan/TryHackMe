**Learning Objective and Pre-requisites**  
If you are new to SIEM, please complete the [[Introduction to SIEM]]. This room covers the following learning objectives:

- Splunk overview
- Splunk components and how they work
- Different ways to ingest logs
- Normalization of logs

## Splunk Components

Splunk has three main components: Forwarder, Indexer and Search Head.

#### Splunk Forwarder

It's a lightweight agent installed on the endpoint (also System or OS) intended to be monitored. The main task of Forwarder is to collect the data and send it to Splunk instance. It doesn't affect endpoint's performance as it takes very few resources to process. Key data sources are:

- Web Server that generates Web trafic.
- Windows machine generating Windows Event Logs, Powershell and Sysmon data.
- Linux host generating host-centric logs.
- Database generating DB connection requests, responses and errors

#### Splunk Indexer

It plays the main role in processing data it receives from forwarders. It takes the data, normalizes it into field-value pairs, determines the datatype of the data and stores the as events. Processed data is easy to search and analyze

#### Search Head

it's the place within the Search & Reporting App where users can search the indexed logs as shown below. When the user searches for a term or uses a Search language named as Splunk Search Processing Language (SSPL), the request is sent to the Splunk Indexet and the relevant events are returened in the form of field-value pairs.

Search Head also provides the ability to transform the results into presentable tables, visualizations like pie-chart, bar-chart and column-chart, as shown below:

#### Questions:

1. Which component is used to collect and send data over the Splunk instance?
   **Answer:** Forwarder

## Navigating Splunk

#### Splunk Bar

When you access Splunk, you will see the default home screen. The top panel is the Splunk bar.
In the Splunk Bar you can see system-level messages ( Messages ), configure the Splunk instance ( Settings ), review the progress of jobs ( Activity ), miscellaneous information such as tutorials ( Help ) and a search future ( Find )

The ability to switch between installed Splunk apps instead of using the Apps panel can be achieved from the Splunk Bar, like in the image below.

#### Apps panel

Next is the Apps panel, we can see there apps installed on this instance. The default pre-installed App is **Search & Reporting**.

#### Explore Splunk

The next section is **Explore Splunk**. This panel contains quick links to add data to the Splunk isntance

#### Splunk Dashboard

The last section is the Home Dashboard. By default, no dashboards are displayed. You can choose from a range of dashboards readily available within your Splunk instance.

#### Questions:

1. In the Add Data tab, which option is used to collect data from files and ports?
   **Answer:** Monitor

## Adding Data

Data sources can be grouped into categories. Below is the chart of available sources:
As shown above, it has a total of 5 steps to successfully upload the data.

1. **Select Source** -> Where we select the Log source.
2. **Select Source Type** -> Select what type of logs are being ingested.
3. **Input Settings** ->Select the index where these logs will be dumped and hostName to be associated with the logs.
4. **Review** -> Review all the gif
5. **Done** -> Final step, where the data is uploaded successfully and ready to be analyzed.

#### Questions:

1. Upload the data attached to this task and create an index "VPN_Logs". How many events are present in the log file?
   **Answer:** 2862
2. How many log events by the user **Maleena** are captured?
   **Answer:** 60
3. What is the name associated with IP 107.14.182.38?
   **Answer:** Smith
4. What is the number of events that originated from all countries except France?
   **Answer:** 2814
5. How many VPN Events were observed by the IP 107.3.206.58?
   **Answer:** 14

## Conclusion

In this room, we explored Splunk, its components, and how it works. Please check the following Splunk walkthrough and challenge rooms to understand how Splunk is effectively used in investigating the incidents.

- [[Incident handling with Splunk]]
- [[Investigating with Splunk]]
