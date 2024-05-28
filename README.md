# SC Automation Script

## Overview

This script is designed to automate responses in Zendesk side conversations (SC) based on the content of the last message. It leverages OpenAI's GPT-4 to classify the last message as either meaningful or meaningless and handles the ticket accordingly. The script also updates the ticket status, logs actions in Google Sheets, and ensures the workflow is smooth and efficient.

## Requirements

- Python 3.6 or higher
- Required Python packages:
  - `requests`
  - `openai`
  - `python-dotenv`
  - `gspread`
  - `google-auth`
- Environment variables for authentication
- A Google Cloud Service Account JSON file for accessing Google Sheets

## Setup

### Environment Variables

Create a `.env` file in the root directory with the following variables:

```
OPENAI_API_KEY=your_openai_api_key
ZENDESK_API_KEY=your_zendesk_api_key
```

### Google Sheets Credentials

Ensure you have a Google Cloud Service Account JSON file (e.g., `gssecret001.json`). This file is required to authenticate and access the Google Sheets API.

## Script Structure

### Authentication

The script starts by loading environment variables for OpenAI and Zendesk API keys and authenticating with Google Sheets.

```python
from dotenv import load_dotenv
import os
import openai
import gspread
from google.oauth2.service_account import Credentials

load_dotenv()

OPENAI_API_KEY = os.getenv('OPENAI_API_KEY')
ZENDESK_API_KEY = os.getenv('ZENDESK_API_KEY')

openai.api_key = OPENAI_API_KEY

SCOPES = ['https://www.googleapis.com/auth/spreadsheets']
CREDENTIALS_FILE = 'gssecret001.json'
gscredentials = Credentials.from_service_account_file(CREDENTIALS_FILE, scopes=SCOPES)
```

### Functions

#### Reply in Side Conversation

Sends a reply in a side conversation.

```python
def reply_in_SC(credentials, recipients, sc_reply, sc_id, ticket_number):
    # Function implementation...
```

#### Put Ticket On-Hold

Puts the ticket on hold with a specific reason and duration.

```python
def put_ticket_on_hold(credentials, hold_value, onhold_reason, ticket_number, SC_Status):
    # Function implementation...
```

#### Call GPT

Calls the GPT-4 API to classify the last message.

```python
def call_gpt(filename):
    # Function implementation...
```

#### Add and Remove Tags

Adds or removes specific tags from the ticket.

```python
def add_atlas_ticket_sc_dont_wakeup_tag(credentials, ticket_number):
    # Function implementation...

def remove_atlas_ticket_sc_dont_wakeup_tag(credentials, ticket_number):
    # Function implementation...
```

#### Write to Google Sheets

Logs actions in a Google Sheet.

```python
def write_to_gsheet(gscredentials, current_time, ticket_number, ticket_brand, SC_Status, global_gpt_reply):
    # Function implementation...
```

#### Open Ticket

Opens the ticket for further review.

```python
def put_ticket_on_open(credentials, ticket_number, SC_Status):
    # Function implementation...
```

### Main Handler

The main handler function processes incoming events, determines the necessary actions, and updates the ticket accordingly.

```python
def lambda_handler(event, context):
    try:
        # Initialization and data retrieval...
        
        # Logic to handle different scenarios...
        
    except Exception as e:
        print(f"Lambda exception occurred: {str(e)}")
        SC_Status = "I encountered a bit of a snag! So, I am leaving the ticket open, removing the sc_esc tag and adding the tempo_unsuspend tag for a manual review by an agent."
        write_to_gsheet(gscredentials, current_time, ticket_number, ticket_brand, SC_Status, global_gpt_reply)
        put_ticket_on_open(credentials, ticket_number, SC_Status)
```

## Usage

Deploy this script as a Lambda function or run it as a standalone script to automate side conversation handling in Zendesk. Ensure all dependencies are installed and environment variables are set correctly before running the script.

## Notes

- Ensure proper permissions are set for the Google Cloud Service Account to access the Google Sheet.
- Test the script thoroughly in a sandbox environment before deploying it to production.
