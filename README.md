# Custom-Cursor-Chrome-Extension
create a custom cursor Google Chrome extension. The ideal candidate will have experience in Chrome extension development and a strong understanding of JavaScript, HTML, and CSS. The extension should allow users to select and apply custom cursors easily.
---------------
To create a Chrome Extension that allows users to select and apply custom cursors easily, we'll follow a step-by-step approach. The extension will consist of a popup interface where users can select custom cursor styles and apply them to the current webpage.
Steps for the Chrome Extension:
1. Setup and File Structure:

Here is a simple folder structure for the extension:

custom-cursor-extension/
├── manifest.json
├── popup.html
├── popup.css
├── popup.js
├── content.js
└── assets/
    └── cursor1.png
    └── cursor2.png
    └── cursor3.png

2. Manifest File (manifest.json)

The manifest.json file is necessary to define the extension and its permissions.

{
  "manifest_version": 3,
  "name": "Custom Cursor Extension",
  "description": "Change the cursor style for your webpage.",
  "version": "1.0",
  "permissions": [
    "activeTab"
  ],
  "action": {
    "default_popup": "popup.html",
    "default_icon": {
      "16": "assets/cursor1.png",
      "48": "assets/cursor1.png",
      "128": "assets/cursor1.png"
    }
  },
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"]
    }
  ],
  "host_permissions": [
    "http://*/*",
    "https://*/*"
  ]
}

3. Popup HTML (popup.html)

This file defines the user interface (UI) of the popup that the user interacts with.

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Custom Cursor</title>
    <link rel="stylesheet" href="popup.css">
</head>
<body>
    <h2>Select Your Cursor</h2>
    <div id="cursor-options">
        <img src="assets/cursor1.png" alt="cursor1" class="cursor-option" data-cursor="assets/cursor1.png">
        <img src="assets/cursor2.png" alt="cursor2" class="cursor-option" data-cursor="assets/cursor2.png">
        <img src="assets/cursor3.png" alt="cursor3" class="cursor-option" data-cursor="assets/cursor3.png">
    </div>
    <button id="reset-cursor">Reset Cursor</button>

    <script src="popup.js"></script>
</body>
</html>

4. Popup CSS (popup.css)

This file will define the styling for the popup UI.

body {
    font-family: Arial, sans-serif;
    width: 200px;
    text-align: center;
}

#cursor-options {
    display: flex;
    justify-content: space-around;
    margin-bottom: 10px;
}

.cursor-option {
    width: 30px;
    height: 30px;
    cursor: pointer;
}

#reset-cursor {
    margin-top: 10px;
    padding: 5px 10px;
    background-color: #4CAF50;
    color: white;
    border: none;
    cursor: pointer;
}

#reset-cursor:hover {
    background-color: #45a049;
}

5. Popup JavaScript (popup.js)

The popup.js file will handle interactions within the popup, such as setting a new cursor or resetting the cursor.

// Wait for the DOM to load before attaching event listeners
document.addEventListener('DOMContentLoaded', function () {
  const cursorOptions = document.querySelectorAll('.cursor-option');
  const resetCursorButton = document.getElementById('reset-cursor');

  // Add event listeners for each cursor option
  cursorOptions.forEach(option => {
    option.addEventListener('click', function () {
      const cursorPath = option.getAttribute('data-cursor');
      // Send the selected cursor path to the content script
      chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
        chrome.scripting.executeScript({
          target: { tabId: tabs[0].id },
          func: changeCursor,
          args: [cursorPath]
        });
      });
    });
  });

  // Reset cursor to default
  resetCursorButton.addEventListener('click', function () {
    chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
      chrome.scripting.executeScript({
        target: { tabId: tabs[0].id },
        func: resetCursor
      });
    });
  });
});

// Function to change the cursor
function changeCursor(cursorPath) {
  document.body.style.cursor = `url(${cursorPath}), auto`;
}

// Function to reset cursor to default
function resetCursor() {
  document.body.style.cursor = 'default';
}

6. Content Script (content.js)

This file interacts with the webpage content to apply the custom cursor style.

// content.js - Injected into the webpage, to modify the cursor style

// Apply the custom cursor when the script is loaded
chrome.runtime.onMessage.addListener(function (request, sender, sendResponse) {
  if (request.action === 'applyCursor') {
    document.body.style.cursor = `url(${request.cursorPath}), auto`;
  }
});

7. Background Script (background.js)

You may not need a complex background script in this case, but it can be useful for handling state or managing tabs. Here's a simple placeholder:

// background.js
chrome.runtime.onInstalled.addListener(function () {
  console.log("Custom Cursor Extension Installed");
});

8. Testing the Extension:

To test the extension:

    Open Chrome.
    Go to chrome://extensions/.
    Enable "Developer mode" on the top right.
    Click "Load unpacked" and select the folder containing the extension files.
    Once the extension is loaded, you should see the extension icon in the Chrome toolbar.
    Click the icon to open the popup, select a custom cursor, and see it applied to the current webpage.

9. Conclusion:

This Chrome extension allows users to select and apply custom cursors to any webpage. It provides a simple interface for managing cursors and ensures that the extension operates on the current webpage content by using Chrome's content script and popup mechanism.

This can be further enhanced by adding features like persistent cursor preferences across sessions, user settings, or more advanced cursor customization options.
