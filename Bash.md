#!/bin/bash

# Check if a log file argument is provided
if [ $# -ne 1 ]; then
  echo "Usage: $0 <log_file>"
  exit 1
fi

# Assign the log file name provided as an argument
log_file="$1"

# Initialize variables to store action counts for each user
declare -A login_count
declare -A click_count
declare -A logout_count

# Read and process the log file line by line
while IFS=, read -r timestamp user_id action; do
  # Remove any leading/trailing whitespace from action
  action=$(echo "$action" | tr -d ' ')

  # Update action counts based on the action type
  case "$action" in
    login)
      ((login_count[$user_id]++))
      ;;
    click)
      ((click_count[$user_id]++))
      ;;
    logout)
      ((logout_count[$user_id]++))
      ;;
  esac
done < "$log_file"

# Create the CSV report file
report_file="user_activity_report.csv"
echo "user_id,login_count,click_count,logout_count" > "$report_file"

# Write user activity counts to the report file
for user_id in "${!login_count[@]}"; do
  login="${login_count[$user_id]:-0}"
  click="${click_count[$user_id]:-0}"
  logout="${logout_count[$user_id]:-0}"
  echo "$user_id,$login,$click,$logout" >> "$report_file"
done

echo "Report generated and saved as '$report_file'."
