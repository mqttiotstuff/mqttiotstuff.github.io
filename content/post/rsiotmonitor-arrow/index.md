+++
title = "Archiving events easily"
description = "using IOT, or ambiant technology as smart home lead to storing lots of events"
date = "2023-01-07"
aliases = ["software"]
author = "Mqtt IOT Stuff"
thumbnail = ""

tags = [
    "software", "rust", "apache arrow"
]
categories = [
    "application","iot","archiving"
]

+++

When expanding the intelligence and capabilities of a smart home, it becomes essential to have access to historical data. This data plays a crucial role in triggering new behaviors based on past events and triggers. By archiving these events, it becomes possible to enhance and fine-tune the smart home's behavior, while also recognizing the inherent value of maintaining a historical record.

When faced with the challenge of storing a large volume of events, it can be particularly daunting for individuals with limited knowledge in computer science or those who prefer not to spend excessive time administering complex platforms for event archiving. Consequently, it is worth reevaluating the methods available for archiving events.

In today's landscape, numerous specialized software solutions have emerged, specifically designed for storing metrics and events, utilizing cutting-edge storage and compression technologies. Prominent names like InfluxDB readily come to mind, along with many others. These software offerings also provide convenient querying capabilities for efficient data analysis. With these considerations in mind, the key factors to consider when selecting the optimal method are as follows:

-Fast storage: Prioritize a solution that offers swift and efficient event storage to handle the high volume of events effectively.
-Painless administration: Seek a software that simplifies the administrative tasks associated with maintaining the event archive, minimizing the need for extensive time and effort.
-Ability to query and analyze data: Choose a software that offers robust querying and analytical features, enabling seamless extraction of insights from the stored data and events.
-Open standards for data access: Ensure that the chosen method adheres to open standards, allowing easy access and compatibility for reading the data.

In the past, alternative solutions such as RRD (Round Robin Database) were available, which allowed for the storage of metrics and associated statistics (e.g., count, minimum, maximum, standard deviation) at multiple scales. These solutions were quite efficient in their time. However, it is also crucial to archive text elements to ensure compatibility with MQTT brokers.

As these thoughts occupied my mind, I conducted tests using big data file formats like ORC or Parquet. Unfortunately, I found myself dissatisfied with the associated elements as they often required a dedicated platform for executing analysis. Despite benefiting from columnar storage, inline compression, and local statistics, these file formats were not meeting my needs.

Consequently, there arose a necessity for simplifying the usage of data, finding a solution that offers greater convenience and usability.

## Arrow echosystem

The Apache Arrow ecosystem emerged as a solution to address these challenges. Originating from DreamIO and now widely adopted, this complementary data format alongside Parquet helps bridge the gap. One notable project within this ecosystem is Datafusion, which offers direct SQL querying of the Arrow format without the need for extensive infrastructure. The performance of Datafusion is remarkable, providing a seamless and efficient querying experience.

Integrating the Apache Arrow ecosystem into my daily monitoring system, RSIoTMonitor (the next version of IoTMonitor), proved to be a straightforward process. Thanks to the availability of Rust libraries, I was able to seamlessly incorporate the functionality. To optimize performance and separate the "hot" and "cold" analysis, I utilized a "Hot" LevelDB database for real-time data processing. Additionally, I implemented date-based archives by storing Parquet files in a dedicated folder. This approach allowed for efficient data management and analysis within RSIoTMonitor.

With minimal effort, the day's archive is automatically written every night, triggering the cold analysis in the process. Furthermore, I am currently working on developing an additional driver to introduce "hot data access" capabilities to the framework. This enhancement will enable faster and more immediate access to real-time data within the RSIoTMonitor system. 

In this addition, Rust language, offers in this case a very good fit for dev to production.


## Some examples


Given the parquet files in the archive_history folder :

```

use@fhome:~/iot/history_archive$ ll
total 588336
drwxrwxr-x 2 use use     4096 Jun 25 02:00 ./
drwxrwxr-x 6 use use     4096 Jun 25 16:01 ../
-rw-rw-r-- 1 use use 28174630 Jun  6 02:00 events_2023-06-06-1686009600.parquet
-rw-rw-r-- 1 use use 26493253 Jun  7 02:00 events_2023-06-07-1686096000.parquet
-rw-rw-r-- 1 use use 26771606 Jun  8 02:00 events_2023-06-08-1686182400.parquet
-rw-rw-r-- 1 use use 27431061 Jun  9 02:00 events_2023-06-09-1686268800.parquet
-rw-rw-r-- 1 use use 29093095 Jun 10 02:00 events_2023-06-10-1686355200.parquet
-rw-rw-r-- 1 use use 28914133 Jun 11 02:00 events_2023-06-11-1686441600.parquet
-rw-rw-r-- 1 use use 28307320 Jun 12 02:00 events_2023-06-12-1686528000.parquet
-rw-rw-r-- 1 use use 28134097 Jun 13 02:00 events_2023-06-13-1686614400.parquet
-rw-rw-r-- 1 use use 28915076 Jun 14 02:00 events_2023-06-14-1686700800.parquet
-rw-rw-r-- 1 use use 27027962 Jun 15 02:00 events_2023-06-15-1686787200.parquet
-rw-rw-r-- 1 use use 26559821 Jun 16 02:00 events_2023-06-16-1686873600.parquet
-rw-rw-r-- 1 use use 26985635 Jun 17 02:00 events_2023-06-17-1686960000.parquet
-rw-rw-r-- 1 use use 27142578 Jun 18 02:00 events_2023-06-18-1687046400.parquet
-rw-rw-r-- 1 use use 29030324 Jun 19 02:00 events_2023-06-19-1687132800.parquet
-rw-rw-r-- 1 use use 36436260 Jun 20 02:00 events_2023-06-20-1687219200.parquet
-rw-rw-r-- 1 use use 33359918 Jun 21 02:00 events_2023-06-21-1687305600.parquet
-rw-rw-r-- 1 use use 33371964 Jun 22 02:00 events_2023-06-22-1687392000.parquet
-rw-rw-r-- 1 use use 34330290 Jun 23 02:00 events_2023-06-23-1687478400.parquet
-rw-rw-r-- 1 use use 38232474 Jun 24 02:00 events_2023-06-24-1687564800.parquet
-rw-rw-r-- 1 use use 37686337 Jun 25 02:00 events_2023-06-25-1687651200.parquet
-rw------- 1 use use     3483 Jun 24 13:54 .history


``` 


launching the datafusion-cli command line :

```
use@fhome:~/iot/history_archive$ ~/.cargo/bin/datafusion-cli 
DataFusion CLI v25.0.0
❯ select count(*) from '.';
+-----------------+
| COUNT(UInt8(1)) |
+-----------------+
| 61705884        |
+-----------------+
1 row in set. Query took 0.268 seconds.
❯ 

```

Computing the number of events for the given archive volume (575MB) is remarkably fast. To explore more details about the query language and additional capabilities of Apache Arrow Datafusion, I recommend visiting their official website at https://arrow.apache.org/datafusion/. The website provides comprehensive information about the query language, features, and facilities offered by Apache Arrow Datafusion. It serves as a valuable resource for understanding and leveraging the full potential of this powerful tool.