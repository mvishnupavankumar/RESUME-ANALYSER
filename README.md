```markdown
# 🤖 Telegram Resume Analyzer Workflow using n8n

## 📖 Description

- 💬 For beginners: This workflow automates the resume analysis process by taking a job description and a resume from Telegram, extracting text from both, comparing them using a language model, and then saving the analysis results to a Google Sheet. It also sends a link to the Google Sheet back to the user via Telegram.
- 💻 For tech users: This workflow uses the Telegram Trigger node to receive input, Google Sheets to store output, Mistral AI for OCR, and Google Gemini for semantic analysis. It also uses various data transformation nodes (Set, Code) to prepare the data for each step.

## ⚙️ How It Works (step-by-step with emojis)

### 🔹 1. Telegram Trigger (TAKES RESUME) 🤖

- **Simple**: Listens for a Telegram message containing a resume file.
- **Tech**: Uses the `Telegram Trigger` node configured to listen for updates of type "message".

### 🔹 2. Telegram (TAKE GOOGLE SHEETS NAME) 💬

- **Simple**: Asks the user to enter the name of the Google Sheet to create.
- **Tech**: Uses the `Telegram` node with the `sendAndWait` operation and `freeText` response type.

### 🔹 3. Telegram (TAKE JOB DESCRIPTION) 📝

- **Simple**: Asks the user to send the job description they want to compare the resume against.
- **Tech**: Uses the `Telegram` node with the `sendAndWait` operation and `freeText` response type.

### 🔹 4. Set Node (EXTRACT DESCRIPTION) 🧱

- **Simple**: Saves the job description that the user provided.
- **Tech**: Uses the `Set` node to assign the Telegram message data to a variable named "description".

### 🔹 5. Code Node (CLEAN IT) 💻

- **Simple**: Cleans up the job description text.
- **Tech**: Uses a `Code` node with JavaScript to remove emojis, newlines, special formatting, and non-ASCII characters from the job description.

### 🔹 6. Telegram (DOWNLOAD RESUME) 📥

- **Simple**: Downloads the resume file sent via Telegram.
- **Tech**: Uses the `Telegram` node with the `file` resource to download the file using its file ID extracted from the trigger message. It dynamically determines if the input is a photo or document and uses the appropriate file_id.

### 🔹 7. Set Node (EXTRACT LINK) 🔗

- **Simple**: Extracts the direct URL to the downloaded resume file from Telegram.
- **Tech**: Uses the `Set` node to create a URL based on the Telegram bot token and the `file_path` returned when downloading the file.

### 🔹 8. HTTP Request (EXTRACT TEXT FROM OCR) 🌐

- **Simple**: Extracts text from the resume using OCR (Optical Character Recognition).
- **Tech**: Sends a POST request to the Mistral AI OCR API, providing the document URL.  It dynamically selects `image_url` or `document_url` based on the mime type of the file.

### 🔹 9. Set Node (TEXT) 🧱

- **Simple**: Saves the extracted resume text.
- **Tech**: Uses the `Set` node to store the markdown text from the OCR response.

### 🔹 10. Code Node (CLEAN IT) 💻

- **Simple**: Cleans up the extracted text from the resume.
- **Tech**: Uses a `Code` node with JavaScript to remove emojis, newlines, special formatting, and non-ASCII characters from the resume text.

### 🔹 11. HTTP Request (COMPARES AND ANALYSIS) 🧠

- **Simple**: Sends the cleaned job description and resume text to a language model for analysis.
- **Tech**: Sends a POST request to the Google Gemini API with a prompt instructing the model to compare the job description and resume, extract relevant keywords for 10 categories, mark matched and unmatched keywords, and provide an overall match score.

### 🔹 12. Set Node (EXTRACT ANALYSIS) ⚙️

- **Simple**: Extracts the Google Sheet name and date.
- **Tech**: Uses the `Set` node to store the Google Sheet name and date, for use in naming the new Google Sheet.

### 🔹 13. Set Node (EXTRAT ANALYSIS) ⚙️

- **Simple**: Extracts the AI analysis text.
- **Tech**: Uses the `Set` node to store the analysis text from the Google Gemini response.

### 🔹 14. Code Node (CLEANS IT) 💻

- **Simple**: Transforms extracted AI analysis into a usable format.
- **Tech**: Uses a `Code` node with JavaScript to extract and parse the JSON block contained in the analysis response.

### 🔹 15. Code Node (MAKE A ARRAY) 🛠️

- **Simple**: Creates an array of the analysis.
- **Tech**: Uses a `Code` node with JavaScript to create an array from the category analysis.

### 🔹 16. Code Node (CLAENS IT) 🛠️

- **Simple**: Cleans the array with all the analysis results.
- **Tech**: Uses a `Code` node with JavaScript to clean the data.

### 🔹 17. Code Node (MAKE OVERALL REVIEW) 📝

- **Simple**: Generates a review.
- **Tech**: Uses a `Code` node with JavaScript to generate a review with all the analysis results.

### 🔹 18. Create spreadsheet (Create spreadsheet) 📝

- **Simple**: Creates a new Google Sheet with the name provided by the user.
- **Tech**: Uses the `Google Sheets` node to create a new spreadsheet.  The spreadsheet's title is dynamically generated using data provided by the user.

### 🔹 19. Code Node (EXTRACT ID) 🆔

- **Simple**: Extracts the ID of the newly created Google Sheet.
- **Tech**: Uses a `Code` node to extract the `spreadsheetId` from the output of the `Create spreadsheet` node.

### 🔹 20. Append row in sheet (Append row in sheet) ➕

- **Simple**: Adds rows to the Google Sheet with the analysis results.
- **Tech**: Uses the `Google Sheets` node to append rows to the Google Sheet, using the category, matched keywords, unmatched keywords, and match percentage.

### 🔹 21. Code Node (GENERATE LINK) 🔗

- **Simple**: Generates the URL of the Google Sheet.
- **Tech**: Uses a `Code` node to create the URL to the Google Sheet, using the spreadsheet ID.

### 🔹 22. Telegram (Send a text message) 💬

- **Simple**: Sends the link to the Google Sheet to the user via Telegram.
- **Tech**: Uses the `Telegram` node to send a text message containing the Google Sheet URL.

### 🔹 23. Merge Node (Merge) ➕

- **Simple**: Merges data.
- **Tech**: Uses a `Merge` node to merges data.

## 🔁 Replace the following before running:

💬 `REPLACE_ME`
Replace this with your personal Telegram chat ID inside the Telegram nodes.

🤖 `YOUR_TELEGRAM_BOT_TOKEN`  
Add your bot token to the Telegram credentials in n8n before sending messages.

🔑 `YOUR_HEYGEN_API_KEY`  
Replace this with your Mistral AI API key in the HTTP Request node.

🔑 `apiKey: YOUR_NEWSDATA_IO_API_KEY`  
Replace this with your Google Gemini API key in the HTTP Request node. You will need to enable the Gemini API in your Google Cloud project.

🪪 `Google Sheets account`  
Configure your Google Sheets credentials in n8n under the Credentials section.

## 🧪 Testing Instructions

✅ Open n8n at http://localhost:5678  
🔧 Click “Import Workflow”  
➕ Paste the JSON into the import dialog  
🤖 Start the "TAKES RESUME" Telegram trigger by sending it any message (this registers the webhook)  
💬 Send the bot a resume (PDF, JPG, PNG) and a job description.  Follow the prompts.
🧪 Check the execution log to troubleshoot issues  
🌐 Verify the Google Sheet is created and populated with data.
💬 Check your Telegram for the link to the generated Google Sheet.

## 💡 Tips for Beginners

💡 Use the “Credentials” tab to set up Telegram, Google Sheets, and API keys  
💡 Check node outputs after each step to verify data  
💡 Adjust the analysis prompt to fine-tune matching accuracy
💡 Consider adding error handling for invalid inputs or API failures
```