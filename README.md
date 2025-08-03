# RESUME-ANALYSER
🤖 Resume Analyzer Bot with n8n
This n8n workflow creates a Telegram bot that analyzes a job description and a candidate's resume, providing a detailed match percentage and a full report in a new Google Sheet. 📊

📖 Description
Are you a recruiter or job seeker looking to quickly evaluate how well a resume matches a job description? This workflow is for you!

The bot will ask you for a job description and a resume, then use an AI to analyze and compare the two documents. The results, including matched/unmatched keywords, match percentages for various categories, and an overall score, are automatically saved to a new Google Sheet and a link is sent directly to you via Telegram.

This automation is built using:

Telegram: To interact with the user, taking inputs and delivering the final report link.

Mistral AI: Uses a powerful OCR (Optical Character Recognition) model to extract text from the resume PDF or image file.

Google Gemini: Utilizes a generative AI model to compare the resume and job description, then generate a detailed JSON report.

Google Sheets: To store the final, clean, and organized analysis in a new spreadsheet for easy review.

⚙️ How It Works (step-by-step with emojis)
🔹 1. Telegram Trigger 🔔
Simple: The workflow starts when you send a message to the bot.

Tech: Uses the Telegram Trigger node, configured to listen for incoming messages.

🔹 2. Take Google Sheets Name 📝
Simple: The bot asks you to provide a name for the new Google Sheet.

Tech: Uses the Telegram node with the sendAndWait operation to prompt the user for free-text input and store the response.

🔹 3. Take Job Description 📜
Simple: The bot prompts you to paste the job description text.

Tech: Another Telegram node with sendAndWait captures the job description.

🔹 4. Extract Description & Clean It 🧹
Simple: The workflow takes the job description you provided and cleans it up.

Tech: A Set node extracts the text, followed by a Code node that uses JavaScript to remove emojis, special characters, and extra spaces, ensuring the text is clean for the AI.

🔹 5. Download Resume & Extract Link 📥
Simple: The workflow downloads the resume file you sent (PDF or image).

Tech: A Telegram node downloads the file. A subsequent Set node dynamically constructs a full URL to the file by combining the Telegram API URL with the file path.

🔹 6. Extract Text from OCR 🖼️
Simple: The workflow uses AI to read the text from the resume file.

Tech: An HTTP Request node sends the resume file URL to the Mistral AI OCR API, which extracts all text content and returns it as a markdown string.

🔹 7. Clean Resume Text 🧼
Simple: Similar to the job description, this step cleans the text extracted from the resume.

Tech: Set and Code nodes process the OCR output to remove formatting, newlines, and other unwanted characters, preparing it for the analysis.

🔹 8. Compares and Analysis 🧠
Simple: This is where the magic happens! The AI compares the two documents.

Tech: An HTTP Request node sends both the clean job description and the clean resume text to the Google Gemini AI API with a detailed prompt. Gemini analyzes the texts and returns a structured JSON output with all the matching data.

🔹 9. Clean & Structure Data 📈
Simple: The raw AI output is converted into a structured format.

Tech: A series of Code nodes parse the JSON from Gemini, extract the match data for each category (Technical Skills, Soft Skills, etc.), and prepare it as a clean array of objects.

🔹 10. Make Overall Review & Create Spreadsheet 📝
Simple: Calculates an overall match score and rating, and creates a new Google Sheet to store the results.

Tech: A Code node calculates the average match percentage across all categories. This data, along with the individual category data, is used by a Google Sheets node to create a new spreadsheet and append the row-by-row analysis.

🔹 11. Generate Link & Send Message 🔗
Simple: The workflow sends you the link to the new Google Sheet with the full analysis.

Tech: Code nodes get the new spreadsheet ID and create a shareable URL. A final Telegram node sends this link as a message to your chat.

🔁 Replace the following before running:
🔐 YOUR_TELEGRAM_CHAT_ID
Replace this with your personal Telegram chat ID inside the TAKE GOOGLE SHEETS NAME and Send a text message nodes.

🤖 YOUR_TELEGRAM_BOT_TOKEN
Add your bot token to the Telegram credentials in n8n under the Credentials section.

🪪 YOUR_MISTRAL_API_KEY
Replace this with your Mistral AI API key in the Authorization header of the EXTRACT TEXT FROM OCR node.

🔑 YOUR_GEMINI_API_KEY
Add your Google Gemini API key to the X-goog-api-key header in the COMPARES AND ANALYSIS node.

🔐 YOUR_GOOGLE_SHEETS_CREDENTIALS
Configure your Google Sheets credentials in n8n to allow the workflow to create and write to new spreadsheets.

🧪 Testing Instructions
✅ Open n8n at http://localhost:5678
🔧 Copy the JSON workflow and click “Import Workflow” in your n8n instance.
⚠️ Configure all credentials as mentioned above.
🚀 Run the workflow by sending a message to your Telegram bot.
🪵 Check the execution log to troubleshoot any issues.

💡 Tips for Beginners
💡 Make sure your Telegram bot is properly set up with a webhook or polling to receive messages.
💡 Check node outputs after each step to verify data is being processed correctly.
💡 When running the workflow, ensure the API keys for Mistral and Gemini have the necessary permissions.
