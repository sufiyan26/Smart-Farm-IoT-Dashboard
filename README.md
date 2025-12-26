# Smart Farm IoT – Real-Time Monitoring, Alerts, and Dashboard

This project implements a complete **Smart Farm IoT backend** in Python, from sensor simulation and MQTT messaging to local time‑series storage, anomaly detection, email alerts, and a live web dashboard. It is designed to run entirely on a local machine but is **hardware‑ready** for future ESP32/Arduino integration.

## Core Concept & Objective

- **Goal:** Provide a realistic farm monitoring pipeline that can track temperature, humidity, and soil moisture across multiple zones, detect issues early, and present the data in an operator‑friendly dashboard.
- **Key Idea:** Use MQTT and SQLite to decouple data ingestion, processing, and visualization into separate long‑running services, making the system modular and easy to extend.

## System Architecture

The system is split into three main services:

1. **Sensor Simulator (Publisher)**
   - Python script that simulates multiple zones (e.g., `zone1`, `zone2`, `zone3`).
   - Publishes JSON payloads with `temperature`, `humidity`, and `soil_moisture` every 5 seconds.
   - Uses Paho MQTT client to publish to topics like:
     - `farm/zone1/data`
     - `farm/zone2/data`
     - `farm/zone3/data`

2. **Processor + Alerts + Storage**
   - Subscribes to all `farm/+/data` topics.
   - Validates payloads, adds timestamps, and stores ordered time‑series data in a local **SQLite** database.
   - Evaluates threshold rules such as:
     - Temperature > 30 °C
     - Humidity < 40 %
   - Sends email alerts via SMTP when thresholds are violated.
   - Uses **IsolationForest** (scikit‑learn) on local data to flag anomalies (spikes, drifts) without needing a cloud backend.

3. **Dashboard (Dash/Plotly UI)**
   - Web app that connects to SQLite and renders:
     - Live time‑series charts per zone.
     - Per‑zone filters and time range selection.
     - Visual markers for threshold breaches and anomaly flags.
   - Runs locally (e.g., `http://localhost:8050`) and polls the DB at a fixed interval for updates.

## Project Structure

├── publisher/
│ └── sensor_sim.py # MQTT publisher for multi-zone synthetic data
├── processor/
│ ├── subscriber.py # MQTT subscriber + SQLite writer
│ ├── thresholds.py # Rule-based checks and alert triggers
│ ├── anomalies.py # IsolationForest anomaly scoring
│ └── email_alerts.py # SMTP-based email notifications
├── dashboard/
│ └── app.py # Dash/Plotly dashboard UI
├── db/
│ └── farm.db # SQLite time-series database
└── config/
└── settings.yaml # MQTT, DB, and email configuration


## Example Data Flow

1. `sensor_sim.py` publishes:
{
"zone": "zone1",
"temperature": 31.2,
"humidity": 38.5,
"soil_moisture": 0.42,
"ts": "2025-09-01T10:15:00Z"
}
2. `subscriber.py` receives the message, validates it, and inserts a row into `farm.db`.
3. `thresholds.py` detects temperature > 30 °C and humidity < 40 % → raises an alert.
4. `email_alerts.py` sends an email notification with latest metrics for that zone.
5. `app.py` plots the new point on the dashboard chart for `zone1` with highlight for threshold/anomaly.


## Skills Demonstrated

Python (Paho MQTT, Dash/Plotly, Pandas, NumPy, scikit‑learn) • MQTT topic design • Local time‑series storage with SQLite • Threshold‑based alerting • Basic anomaly detection with IsolationForest • Multi‑service architecture • Edge‑friendly IoT backend design ready for ESP32/Arduino hardware‑in‑the‑loop




