# Backup Plugin Documentation

## Overview

The Backup Plugin is a Python-based tool designed to automate the process of backing up a specified file to a remote server at regular intervals. It ensures that only a defined number of the most recent backups are retained on the server by automatically managing and deleting older files as needed. This helps in maintaining an efficient backup system without manual intervention.

## Requirements

- **Python Version**: Python 3.8 or higher
- **Libraries**: requests, schedule

## Installation

1. **Set Up a Virtual Environment**

    Create and activate a virtual environment to manage project dependencies:

    ```sh
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```

2. **Install Required Libraries**

    Install the necessary Python libraries using the following commands:

    ```sh
    pip install requests schedule
    ```

## Configuration

1. **Create a Configuration File**

    Create a `config.json` file in the root directory of the project with the following structure:

    ```json
    {
      "file_path": "./exampleFile.txt",
      "server_url": "http://your-company-server-url/upload",
      "interval_minutes": 60,
      "max_files": 5
    }
    ```

    - **file_path**: The path to the file you want to back up.
    - **server_url**: The URL of the company's server where the file will be uploaded.
    - **interval_minutes**: The interval (in minutes) at which the file will be uploaded.
    - **max_files**: The maximum number of files to retain on the server.

## Running the Backup Plugin

1. **Configure Backup Plugin**

    Ensure the backup plugin reads from `config.json`:

    ```python
    import json
    import schedule
    import time
    import requests

    # Load configuration from config.json
    with open('config.json', 'r') as config_file:
        config = json.load(config_file)

    # Function to upload the file to the server
    def upload_file():
        with open(config["file_path"], 'rb') as f:
            response = requests.post(f"{config['server_url']}", files={'file': f})
            print(response.json())

    # Schedule the file upload at the specified interval
    schedule.every(config["interval_minutes"]).minutes.do(upload_file)

    # Run the schedule
    while True:
        schedule.run_pending()
        time.sleep(1)
    ```

2. **Start the Backup Plugin**

    Run the backup plugin script to start uploading the specified file at the configured intervals:

    ```sh
    python backup_plugin.py
    ```

## Expected Behavior

- The plugin will periodically upload the specified file to the company's server at the intervals defined in `config.json`.
- Filenames will be timestamped to avoid collisions and to maintain a chronological order on the server.
- When the number of files on the server exceeds the `max_files` limit specified in `config.json`, the server should automatically delete the oldest file to make room for new uploads.

## Summary

By following this documentation, you should be able to set up, configure, and run the backup plugin. This plugin efficiently manages file uploads, ensuring that only a specified number of the most recent files are retained on the company's server. This helps maintain an organized and up-to-date backup system with minimal manual intervention.
