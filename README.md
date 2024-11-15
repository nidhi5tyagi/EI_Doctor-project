# EI_Doctor-project

Here’s a Markdown document describing the project requirements, functionality, and implementation details:

---

# **System Health Monitoring Project**

## **Overview**
This project is designed to monitor the system's health, providing real-time feedback and generating periodic reports. It includes the following functionalities:
1. **Hourly System Health Check**:
   - A Bash script runs every hour via `cron`, checking key system parameters.
   - If a critical condition is detected, an email alert is sent.
   - A CSV file logs system health data.
   
2. **Weekly Report Generation**:
   - Summarizes system health statistics over the week.
   - Sends the report via email to designated recipients.

3. **Interactive Dashboard**:
   - Displays the system's health metrics in an interactive terminal interface using tools like `curses` or `dialog`.

---

## **Project Features**

### 1. **System Health Monitoring**
- **Metrics Monitored**:
  - CPU usage
  - Memory usage
  - Disk space
  - System uptime
  - Process status

- **Critical Condition Detection**:
  - Thresholds for each metric are predefined.
  - If metrics exceed thresholds, an email is triggered with details.

- **Logging**:
  - Logs all monitored data into a CSV file for further analysis.

### 2. **Email Alerts**
- **Triggered by Critical Conditions**:
  - Emails include details about the problematic metric.
- **Configured via `mail` utility**:
  - Uses `sendmail` or SMTP configuration to send alerts.

### 3. **Weekly Report**
- Aggregates health data from the CSV logs.
- Summarizes:
  - Average CPU and memory usage.
  - Maximum and minimum recorded values.
  - System uptime trends.
- Sent as an email attachment.

### 4. **Interactive Dashboard**
- Displays real-time metrics in a terminal interface using tools like:
  - `dialog` for menus and simple displays.
  - `curses` for a richer TUI (Terminal User Interface).

---

## **Implementation Steps**

### 1. **Bash Script for System Health Check**
- **Location**: `/usr/local/bin/system_health.sh`
- **Responsibilities**:
  - Collect system metrics using commands like `top`, `df`, `uptime`, and `ps`.
  - Write metrics to a CSV file (e.g., `/var/log/system_health.csv`).
  - Compare metrics with predefined thresholds and trigger email alerts if needed.

- **Example Snippet**:
  ```bash
  #!/bin/bash

  # Check CPU usage
  cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
  if (( $(echo "$cpu_usage > 90.0" | bc -l) )); then
      echo "Critical: CPU usage is $cpu_usage%" | mail -s "Critical CPU Usage Alert" admin@example.com
  fi

  # Log data to CSV
  echo "$(date),$cpu_usage" >> /var/log/system_health.csv
  ```

### 2. **Cron Job Configuration**
- **Cron File**: `/etc/cron.d/system_health`
- **Cron Entry**:
  ```bash
  0 * * * * root /usr/local/bin/system_health.sh
  ```

### 3. **Weekly Report Script**
- **Location**: `/usr/local/bin/weekly_report.sh`
- **Responsibilities**:
  - Parse the CSV log and calculate averages, max/min values.
  - Generate a summary in a text or HTML file.
  - Send the report as an email attachment.

- **Email Sending Snippet**:
  ```bash
  mail -s "Weekly System Report" -A /var/log/weekly_report.txt admin@example.com < /dev/null
  ```

- **Cron Entry for Weekly Execution**:
  ```bash
  0 9 * * 1 root /usr/local/bin/weekly_report.sh
  ```

### 4. **Interactive Dashboard**
- **Tool**: `dialog` or `curses`
- **Script**: `/usr/local/bin/interactive_dashboard.sh`
- **Features**:
  - Menu for choosing metrics to view.
  - Dynamic updates of CPU, memory, and disk stats.
  - Exit option to close the interface.

- **Example with `dialog`**:
  ```bash
  dialog --title "System Health" --msgbox "CPU: $cpu_usage%\nMemory: $mem_usage%\nDisk: $disk_space%" 10 50
  ```

---

## **Dependencies**
- **Bash**: Primary scripting language.
- **dialog or curses**: For terminal-based GUI.
- **mailutils**: For sending email alerts and reports.
- **cron**: For scheduling hourly and weekly tasks.

---

## **Directory Structure**
```
/usr/local/bin/
    ├── system_health.sh        # Main script for hourly checks
    ├── weekly_report.sh        # Script to generate and send reports
    ├── interactive_dashboard.sh # Script for interactive terminal UI
/var/log/
    ├── system_health.csv       # Log file for system metrics
    ├── weekly_report.txt       # Weekly report file
```

---

## **Setup Instructions**

1. **Install Dependencies**:
   ```bash
   sudo apt update
   sudo apt install dialog mailutils cron
   ```

2. **Deploy Scripts**:
   - Copy the scripts to `/usr/local/bin/` and make them executable:
     ```bash
     chmod +x /usr/local/bin/system_health.sh
     chmod +x /usr/local/bin/weekly_report.sh
     chmod +x /usr/local/bin/interactive_dashboard.sh
     ```

3. **Set Up Cron Jobs**:
   - Add the following entries to `/etc/cron.d/system_health`:
     ```bash
     0 * * * * root /usr/local/bin/system_health.sh
     0 9 * * 1 root /usr/local/bin/weekly_report.sh
     ```

4. **Run Interactive Dashboard**:
   - Execute the interactive script manually when needed:
     ```bash
     /usr/local/bin/interactive_dashboard.sh
     ```

---

## **Future Enhancements**
- Add graphical dashboards (e.g., using Python and libraries like `Tkinter` or `Dash`).
- Store logs in a database for better reporting and querying.
- Add Slack or SMS alerts for critical conditions.

---

This project ensures proactive system monitoring, easy reporting, and user-friendly interaction, making it an essential tool for administrators.
