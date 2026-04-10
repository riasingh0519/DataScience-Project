# Network Traffic Data Cleaning: UCM FibIoT 2024 Dataset

This repository contains the data cleaning and preprocessing pipeline for the **UCM FibIoT 2024** dataset. The project focuses on preparing raw network packet capture (pcap) data for machine learning tasks, specifically aimed at detecting Distributed Denial of Service (DDoS) attacks in IoT environments.

## Project Overview

The primary goal of this notebook (`data_cleaning.ipynb`) is to transform raw, noisy CSV files exported from Wireshark into a standardized, "analysis-ready" format. It handles three specific types of network flood attacks:

| Attack Dataset | Layer | Description |
| :--- | :--- | :--- |
| `HTTP_flood.csv` | Application | Layer 7 DDoS attack targeting web servers via HTTP requests. |
| `ICMP_flood.csv` | Network | Layer 3 flood using Echo Request packets (Ping flood). |
| `SYN_flood.csv` | Transport | Layer 4 TCP attack exploiting the TCP three-way handshake. |

## Cleaning Pipeline

The notebook implements a robust cleaning function that performs the following 11 steps to ensure data integrity:

1.  **Standardize Column Names:** Converts all headers to lowercase and replaces spaces/dots with underscores (e.g., `Source Port` -> `source_port`).
2.  **Drop Indexing Columns:** Removes the Wireshark capture index (`no`) as it does not contribute to statistical analysis.
3.  **Parse Datetime:** Converts time strings into high-precision Pandas datetime objects.
4.  **Handle Missing Ports:** * For portless protocols (ARP, ICMP, LLDP), missing ports are filled with a `-1` sentinel value.
    * For port-based protocols (TCP, UDP), rows with missing ports are dropped.
5.  **Remove Duplicates:** Identifies and removes exact duplicate packet entries.
6.  **Whitespace Removal:** Strips leading/trailing spaces from string values in Source, Destination, and Protocol columns.
7.  **Length Validation:** Filters out packets with invalid (non-positive) lengths.
8.  **Port Range Validation:** Ensures ports are within the valid 0–65535 range.
9.  **Memory Optimization:** Converts the `protocol` column into a Categorical type to significantly reduce memory footprint.
10. **Chronological Sorting:** Reorders all logs by their packet arrival time.
11. **Labeling:** Adds an `attack_type` column to facilitate supervised multi-class classification.

## Dataset Statistics

The raw dataset contains over **44.7 million rows**. After processing, the pipeline maintains high data integrity with minimal row loss (less than 0.01%):

| Dataset | Raw Rows | Cleaned Rows | Rows Removed |
| :--- | :--- | :--- | :--- |
| **HTTP Flood** | 10,799,707 | 10,799,526 | 181 |
| **ICMP Flood** | 11,203,031 | 11,202,929 | 102 |
| **SYN Flood** | 22,780,665 | 22,780,333 | 332 |

## Getting Started

### Prerequisites
* Python 3.8+
* Pandas
* NumPy
* Matplotlib / Seaborn (for visualizations)

### Usage
1. Place your raw data in the following directory structure: `Dataset_UCM_FibIoT2024/`.
2. Open the `data_cleaning.ipynb` notebook in Jupyter or VS Code.
3. Run the cells sequentially to perform the exploratory data analysis and execute the cleaning function.

## Visualizations Included
The notebook generates several plots to verify data quality:
* **Missing Value Analysis:** Charts showing null percentages before cleaning.
* **Protocol Distribution:** Summary of the most frequent protocols found in each attack type (TCP, ICMP, MDNS, etc.).

***
*This project was developed as part of a Data Science investigation into IoT security and network traffic analysis.*
