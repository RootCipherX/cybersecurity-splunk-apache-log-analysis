# 📈 Cybersecurity: Splunk SIEM Apache Log Analysis & Dashboarding

## 📖 Table of Contents
- [Project Overview](#-project-overview)
- [Phase 1: Dashboard Initialization](#phase-1-dashboard-initialization)
- [Phase 2: Data Ingestion](#phase-2-data-ingestion)
- [Phase 3: SPL Queries & Metric Panels](#phase-3-spl-queries--metric-panels)
- [Phase 4: Advanced Visualizations & Mapping](#phase-4-advanced-visualizations--mapping)
- [Final Report Generation](#-final-report-generation)

---

## 📌 Project Overview
This project demonstrates advanced log analysis capabilities using Splunk Enterprise. It details the ingestion of JSON-formatted Apache web server logs, the construction of custom Search Processing Language (SPL) queries, and the creation of a dynamic executive dashboard to monitor web traffic, HTTP status codes, and the geographic origin of incoming requests.

---

## Phase 1: Dashboard Initialization

To begin, a centralized location for visualizing the log data was required. I navigated to the "Search & Reporting" app and initialized a new dashboard workspace.

![Create Dashboard](images/01-create-new-dashboard.png)

Configured the dashboard metadata, naming it "Splunk Dashboard for Web Traffic Logs" and setting its permissions to Private for the initial build phase.

![Dashboard Details](images/02-fill-dashboard-details.png)

The blank dashboard was successfully provisioned and ready for customization.

![Dashboard Created](images/03-dashboard-created.png)

To allow dynamic filtering of log data, an interactive input panel was necessary. I accessed the "Add Input" menu to integrate user controls.

![Add Input](images/04-add-submit-button.png)

Selected the "Time" input module, which allows analysts to filter dashboard metrics based on specific chronological windows.

![Select Time Input](images/05-add-time-panel.png)

Configured the Time Picker properties, binding it to the underlying search queries and defaulting the view to analyze "All time".

![Configure Time Picker](images/06-config-time-panel.png)

Finalized the time input panel by assigning it the standard `time_range` token.

![Apply Time Token](images/07-fill-time-panel.png)

---

## Phase 2: Data Ingestion

With the dashboard foundation laid, the target Apache logs needed to be ingested into the Splunk indexer. Accessed the global "Settings" menu and initiated the "Add Data" sequence.

![Add Data Menu](images/08-to-add-log-file-step1.png)

Selected the "Upload" method to ingest the local log files directly from the host system into the Splunk platform.

![Upload Method](images/09-click-on-upload.png)

Browsed the local storage and successfully uploaded the target `apache_logs.json` file.

![Select File](images/10-click-select-file-button.png)

Progressed through the "Set Source Type" phase, confirming that Splunk correctly parsed the incoming data as `_json` formatted events.

![Set Source Type](images/11-click-next.png)

Modified the Input Settings, explicitly setting the "Host field value" to `Datta-Guru` to ensure all ingested events were properly attributed to the correct simulated web server.

![Configure Host](images/12-click-review-host-req.png)

Reviewed the final data ingestion parameters before committing the data to the default Splunk index.

![Review Submission](images/13-click-submit.png)

The JSON log file was successfully indexed. Clicked "Start Searching" to begin the data analysis phase.

![Start Searching](images/14-start-searching.png)

The main Splunk search interface successfully populated with the raw, parsed Apache log events.

![View Raw Logs](images/15-logs-opened.png)

---

## Phase 3: SPL Queries & Metric Panels

To visualize the data, custom SPL (Search Processing Language) queries were constructed to extract key metrics. 

**Query 1: Total Web Requests**
*   **SPL:** `source="apache_logs.json" host="Datta-Guru" sourcetype="_json" | stats count AS "Total Web Requests"`
*   **Explanation:** This query targets the specific uploaded log file and uses the `stats count` function to calculate the absolute number of events, aliasing the output column for readability.

![Total Requests SPL](images/16-total-web-requests-spl.png)

Selected "Add Panel" and converted the raw statistical output into a "Single Value" visualization for the dashboard.

![Add Single Value Panel](images/17-add-total-web-req-panel.png)

The dashboard successfully displayed the total volume of processed web requests (2,000 events).

![Total Requests Output](images/18-total-web-requests-output.png)

**Query 2: Successful HTTP Responses**
*   **SPL:** `source="apache_logs.json" host="Datta-Guru" sourcetype="_json" method=GET status=200 | stats count AS "Successful Responses"`
*   **Explanation:** This query refines the search by explicitly filtering for standard HTTP `GET` requests that returned a `200 OK` status code. 

![Successful Responses SPL](images/19-successful-responses.png)

Added the refined query to the dashboard as a second Single Value panel, displaying the 1,168 successful connections. *(Note: This workflow was subsequently repeated to filter and display HTTP 4xx Client Errors and HTTP 5xx Server Errors).*

![Add Successful Responses Panel](images/20-add-successful-responses-to-dashboard.png)

---

## Phase 4: Advanced Visualizations & Mapping

To understand the origin and targets of the traffic, advanced visualization modules were implemented.

**Query 3: Top Users by IP Address**
*   **SPL:** `source="apache_logs.json" host="Datta-Guru" sourcetype="_json" | stats count AS IP by ip`
*   **Explanation:** This query counts the frequency of events grouped by the originating `ip` address, passing the data into a Bar Chart visualization to identify the most active clients interacting with the server.

![Top IPs Panel](images/31-total-users-by-ip-panel.png)

The rendered Bar Chart highlights the distribution of request volumes across different client IP addresses.

![Top IPs Chart](images/32-total-users-by-ip-output.png)

**Query 4: Geographic IP Mapping**
*   **SPL:** `source="apache_logs.json" ... method=GET | table ip | iplocation ip | stats count by Country | geom geo_countries featureIdField="Country"`
*   **Explanation:** This advanced query creates a tabular list of IP addresses (`table ip`), uses Splunk's internal geolocation database to map them to physical locations (`iplocation ip`), groups the counts by nation (`stats count by Country`), and finally projects the data onto a visual map using the `geom` command.

![Geographic Mapping SPL](images/33-web-trrafic-by-client-ip-spl.png)

Selected the "Choropleth Map" module to render the geographic data accurately.

![Select Choropleth Map](images/34-web-trrafic-by-client-ip-panel.png)

The rendered map provides immediate visual intelligence regarding the global origin of the inbound web traffic.

![Geographic Map Output](images/35-web-trrafic-by-client-ip-output.png)

---

## 📑 Final Report Generation

With all metrics and visualizations successfully configured, the dashboard was organized, stylized, and saved. The interface provides real-time oversight of critical web infrastructure health.

![Final Dashboard Layout](images/36-final-dashboard.png)

To provide an executive summary of the findings, the dashboard was exported as a static PDF report. The exported report confirms exactly 2,000 total web requests, 1,168 successful responses, 376 client errors, and 376 server errors[cite: 1]. The dashboard visualizations successfully tracked Top Requested URIs, Top Users by IP Address, and Web Traffic by Client IP Addresses[cite: 1].
