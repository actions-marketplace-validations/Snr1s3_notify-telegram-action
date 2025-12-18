# Notify Me Through Telegram

A lightweight **GitHub Composite Action** that sends a Telegram message when a workflow finishes running. It helps you monitor CI/CD results in real time by delivering clear, Markdown-formatted notifications directly to Telegram.

## ✨ Features

* 📣 Sends Telegram notifications on workflow completion
* 📦 Includes repository name, commit message, author, workflow name, and status
* 🟢🔴 Clearly indicates success or failure
* 📝 Uses Markdown formatting for readability
* 🧩 Implemented as a reusable composite action

---

## 📂 Action Definition (`action.yml`)

```yaml
name: Notify Me Through Telegram
description: Send a Telegram message about a workflow run

inputs:
  telegram_token:
    description: Telegram bot token
    required: true
  telegram_chat_id:
    description: Telegram chat ID
    required: true
  message:
    description: Message text (Markdown supported)
    required: true

runs:
  using: composite
  steps:
    - name: Send Telegram notification
      shell: bash
      run: |
        curl -s -X POST "https://api.telegram.org/bot${{ inputs.telegram_token }}/sendMessage" \
          -d chat_id="${{ inputs.telegram_chat_id }}" \
          -d parse_mode=Markdown \
          -d text="${{ inputs.message }}"
```

---

## 🚀 Usage

### 1. Add the Action to Your Repository

Create the following file in your repository:

```
.github/actions/notify-telegram/action.yml
```

Paste the **Action Definition** above into that file.

---

### 2. Configure a Workflow to Trigger Notifications

This example listens for the completion of a workflow named **"Go Test"** and sends a Telegram message with relevant details.

```yaml
name: Telegram Notification

on:
  workflow_run:
    workflows: ["Go Test"] # Change to your workflow names
    types:
      - completed

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Telegram notification
        uses: ./.github/actions/notify-telegram
        with:
          telegram_token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          telegram_chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          message: |
            🔔 *${{ github.repository }}*
            *Commit:* [${{ github.event.workflow_run.head_commit.message }}](${{ github.server_url }}/${{ github.repository }}/commit/${{ github.event.workflow_run.head_commit.id }})
            *Author:* ${{ github.event.workflow_run.head_commit.author.name }}
            *Workflow:* ${{ github.event.workflow_run.name }}
            *Status:* ${{ github.event.workflow_run.conclusion == 'success' && '✅ Success' || '❌ Failure' }}
```

---

## 🔐 Secrets Configuration

Add the following secrets in your repository:

1. Go to **Settings → Secrets and variables → Actions**
2. Add:

| Name                 | Description                                    |
| -------------------- | ---------------------------------------------- |
| `TELEGRAM_BOT_TOKEN` | Bot token from **@BotFather**                  |
| `TELEGRAM_CHAT_ID`   | Chat or channel ID where messages will be sent |

---

## 📝 Notes

* Ensure your Telegram bot has been started (`/start`) and has permission to message the chat
* To monitor multiple workflows, list them in the `workflows` array
* This action supports **Markdown**, so you can fully customize your messages

---

## 🛠 Customization Ideas

* Add timestamps to messages
* Send different messages based on success/failure
* Include links to workflow runs or pull requests

---

📬 **Enjoy real-time CI/CD updates on Telegram!**
