# Slack Daily Attendance Report Workflow

An automated n8n workflow that tracks employee check-ins, check-outs, breaks, and generates daily attendance reports from Slack messages.

## 📋 Overview

This workflow monitors a Slack channel for employee attendance commands (check-in, check-out, step-in, step-out), calculates working hours, accounts for breaks, and sends a daily CSV report with attendance statistics to a designated Slack channel.

## ✨ Features

- **Automatic Attendance Tracking** - Monitors Slack messages for attendance commands
- **Break Time Management** - Tracks step-in/step-out durations (auto-adds 1 hour for missing step-in)
- **Shift-Based Status Calculation** - Determines "On Time", "Late", or "Absent" based on employee shifts
- **Multi-Shift Support** - Handles overnight shifts (e.g., 4 PM to 1 AM)
- **Fuzzy Message Matching** - Recognizes variations like "checkin", "checked in", "stepout"
- **CSV Report Generation** - Exports attendance data with summary statistics
- **Slack Integration** - Automatically posts reports to a configured Slack channel
- **Missing User Handling** - Adds absent employees to the report automatically

## 🚀 Commands

Users type these commands in the specified Slack channel:

| Command | Purpose |
|---------|---------|
| `check in` | Mark arrival at work |
| `check out` | Mark departure from work |
| `step out` | Start a break |
| `step in` | End a break |

**Fuzzy matching supported:** 
- `checkin`, `checked in`, `checkin` → `check in`
- `checkout`, `checked out` → `check out`
- `stepout`, `stepped out` → `step out`
- `stepin`, `stepped in` → `step in`

## 📊 Report Output

The workflow generates a report with:

- **Employee Name** - Real name from Slack profile
- **Date** - Report date
- **Check-In Time** - First check-in of the day
- **Check-Out Time** - Last check-out (`(M)` indicates missing check-out)
- **Work Hours** - Total hours minus breaks
- **Break Duration** - Total break time or "No Break"
- **Status** - On Time, Late, or Absent

Plus summary statistics:
- Total records
- Late check-ins count
- On-time percentage

## ⚙️ Prerequisites

- n8n instance (self-hosted or cloud)
- Slack workspace with admin access
- Slack API token with permissions:
  - `channels:history`
  - `channels:read`
  - `users:read`
  - `chat:write`

## 🔧 Installation

1. **Import the workflow** into your n8n instance
2. **Configure Slack credentials**:
   - Create a Slack app at https://api.slack.com/apps
   - Add required OAuth scopes
   - Install app to your workspace
   - Copy the Bot User OAuth Token
   - Add credentials in n8n (name: `Slack checkbot api token`)

3. **Update channel IDs** in these nodes:
   - `Fetch Daily Messages` - Channel to monitor
   - `Send Report to Slack` - Channel to post reports
   - `Get members of a channel` - Channel to get user list

4. **Update user IDs** in the `check for the absent` node

5. **Configure shifts** in the `Shift assign and Calculate Status` node

## 📝 Configuration

### User Shift Configuration

Edit the `userShifts` object in the "Shift assign and Calculate Status" node:

```javascript
const userShifts = {
  "USER_ID": { start: 16, end: 1 },  // 4 PM to 1 AM (overnight)
  "USER_ID": { start: 13, end: 22 }, // 1 PM to 10 PM
};
