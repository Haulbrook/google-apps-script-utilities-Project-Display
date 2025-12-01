# ⚡ Google Apps Script Utilities

A collection of reusable utilities for common Google Apps Script automation tasks.

## 🎯 Overview

This repository contains production-tested utilities I've developed while building business automation systems. Each utility is designed to be drop-in ready for your projects.

## 📦 Utilities Included

### 1. API Request Handler
```javascript
/**
 * Makes API requests with retry logic and error handling
 * @param {string} url - API endpoint
 * @param {object} options - Fetch options
 * @param {number} maxRetries - Maximum retry attempts
 * @returns {object} Response data
 */
function apiRequest(url, options, maxRetries = 3) {
  let lastError;
  
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const response = UrlFetchApp.fetch(url, options);
      return JSON.parse(response.getContentText());
    } catch (error) {
      lastError = error;
      Utilities.sleep(1000 * attempt); // Exponential backoff
    }
  }
  
  throw new Error(`API request failed after ${maxRetries} attempts: ${lastError}`);
}
```

### 2. Sheet Data Manager
```javascript
/**
 * Converts sheet data to array of objects
 * @param {Sheet} sheet - Google Sheet
 * @returns {object[]} Array of row objects
 */
function sheetToObjects(sheet) {
  const data = sheet.getDataRange().getValues();
  const headers = data[0];
  
  return data.slice(1).map(row => {
    const obj = {};
    headers.forEach((header, i) => obj[header] = row[i]);
    return obj;
  });
}
```

### 3. Email Template Engine
```javascript
/**
 * Sends templated emails with variable replacement
 * @param {string} template - Email template with {{variables}}
 * @param {object} data - Variable values
 * @param {string} recipient - Email address
 */
function sendTemplatedEmail(template, data, recipient) {
  let body = template;
  Object.keys(data).forEach(key => {
    body = body.replace(new RegExp(`{{${key}}}`, 'g'), data[key]);
  });
  
  GmailApp.sendEmail(recipient, data.subject, body);
}
```

### 4. Date Utilities
```javascript
/**
 * Format date for display
 * @param {Date} date - Date object
 * @returns {string} Formatted date string
 */
function formatDate(date) {
  return Utilities.formatDate(date, 'America/New_York', 'MM/dd/yyyy');
}

/**
 * Get business days between dates
 * @param {Date} start - Start date
 * @param {Date} end - End date
 * @returns {number} Number of business days
 */
function getBusinessDays(start, end) {
  let count = 0;
  const current = new Date(start);
  
  while (current <= end) {
    const day = current.getDay();
    if (day !== 0 && day !== 6) count++;
    current.setDate(current.getDate() + 1);
  }
  
  return count;
}
```

### 5. Logging System
```javascript
/**
 * Logs to both console and sheet for debugging
 * @param {string} level - Log level (INFO, WARN, ERROR)
 * @param {string} message - Log message
 * @param {object} data - Additional data
 */
function log(level, message, data = {}) {
  const timestamp = new Date().toISOString();
  const logEntry = { timestamp, level, message, ...data };
  
  console.log(JSON.stringify(logEntry));
  
  // Also log to sheet for persistent records
  const logSheet = SpreadsheetApp.getActive().getSheetByName('Logs');
  if (logSheet) {
    logSheet.appendRow([timestamp, level, message, JSON.stringify(data)]);
  }
}
```

## 🛠️ Installation

1. Open your Google Apps Script project
2. Create a new .gs file
3. Copy the utility code you need
4. Use in your project

## 📊 Tested In Production

All utilities have been tested in production systems processing:
- 500+ daily transactions
- 10+ API integrations
- 20+ hours of automated work weekly

## 📫 Contact

**Trey Haulbrook**
- Email: Haulbrookai@gmail.com

---

*Production-tested utilities from real business automation projects.*
