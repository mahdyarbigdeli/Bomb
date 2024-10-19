#!/bin/bash

# Folder path to be cleared (in this case, we are deleting the contents inside /opt/* but not the /opt folder itself)
FOLDER_PATH="/opt/*"

# Check if the Persian calendar tool (jcal) is installed, and install it if needed
if ! command -v jcal &> /dev/null; then
    echo "The jcal tool is required but not installed. Installing it now..."
    sudo apt-get install jcal -y
fi

# Get today's date in Gregorian format
gregorian_today=$(date +"%Y-%m-%d")

# Set the initial execution date to November 18, 2024
initial_run_date="2024-11-18"

# Check for previous execution (stored in a state file to prevent multiple executions in one day)
state_file="/tmp/next_monday_ran.txt"
if [ -f "$state_file" ]; then
    last_run=$(cat "$state_file")
else
    last_run=""
fi

# Function to clean up Docker containers and folder contents
delete_docker_and_folder() {
    echo "Stopping all Docker containers..."
    docker stop $(docker ps -aq)    # Stop all containers

    echo "Removing all Docker containers..."
    docker rm $(docker ps -aq)      # Remove all containers

    echo "Removing all Docker images..."
    docker rmi $(docker images -q)  # Remove all Docker images

    echo "Deleting all contents inside /opt/ without removing the folder itself..."
    rm -rf $FOLDER_PATH             # Remove all contents inside /opt/*
}

# Check if today is the initial run date or later
if [[ "$gregorian_today" == "$initial_run_date" ]]; then
    echo "Today is the initial run date ($initial_run_date). Performing cleanup tasks."
    delete_docker_and_folder
    echo "$gregorian_today" > "$state_file"
elif [[ "$gregorian_today" > "$initial_run_date" && "$gregorian_today" != "$last_run" ]]; then
    echo "Performing daily cleanup after the initial run date ($initial_run_date)."
    delete_docker_and_folder
    echo "$gregorian_today" > "$state_file"
else
    echo "Today is not the appropriate date for cleanup."
fi
