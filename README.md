# GitHub Webhook Dashboard

A real-time Flask application that receives GitHub webhook events, stores them in MongoDB, and displays them in a beautiful dashboard with 15-second auto-refresh.

## 🚀 Features

- **Real-time Webhook Processing**: Handles GitHub Push, Pull Request, and Merge events
- **MongoDB Integration**: Stores webhook data with proper schema
- **Beautiful UI**: Modern, responsive dashboard with auto-refresh
- **Security**: Optional webhook signature verification
- **Real-time Updates**: Polls data every 15 seconds
- **Event Formatting**: Custom display formats for different GitHub actions

## 📋 Requirements

- Python 3.8+
- MongoDB Atlas account (or local MongoDB)
- GitHub repository for webhook testing

## 🛠️ Setup Instructions

### 1. Clone and Install Dependencies

```bash
# Navigate to your project directory
cd "c:\Users\prInce dabre\Downloads\18s projects\techstax_assignment(python dev)"

# Install dependencies
pip install -r requirements.txt
```

### 2. Configure Environment Variables

1. Copy the example environment file:
   ```bash
   copy .env.example .env
   ```

2. Edit `.env` with your MongoDB connection string:
   ```
   MONGO_URI=mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/github_webhooks?retryWrites=true&w=majority
   WEBHOOK_SECRET=your_optional_secret_here
   ```

### 3. MongoDB Setup

**Driver Selection for MongoDB Atlas:**
- **Driver**: Python
- **Version**: 4.0 or later

Your connection string should look like:
```
mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/github_webhooks?retryWrites=true&w=majority
```

### 4. Run the Application

```bash
python app.py
```

The application will start on `http://localhost:5000`

## 🔗 GitHub Repository Setup

### For `action-repo` (Trigger Repository):

1. Create a new GitHub repository named `action-repo`
2. Go to Settings → Webhooks → Add Webhook
3. Configure:
   - **Payload URL**: `https://your-domain.com/webhook` (or ngrok URL for testing)
   - **Content Type**: `application/json`
   - **Secret**: (optional, same as WEBHOOK_SECRET in .env)
   - **Events**: Select "Push" and "Pull requests"
   - **Active**: ✅ Checked

### For `webhook-repo` (This Repository):

1. Create a new GitHub repository named `webhook-repo`
2. Push this code to that repository

## 📊 API Endpoints

- `GET /` - Main dashboard UI
- `POST /webhook` - GitHub webhook endpoint
- `GET /data` - JSON API for latest events
- `GET /health` - Health check endpoint

## 🔄 Event Processing

### Supported GitHub Events:

1. **Push Events**:
   - Format: "{author} pushed to {to_branch} on {timestamp}"
   - Example: "Travis pushed to staging on 1st April 2021 - 9:30 PM UTC"

2. **Pull Request Events**:
   - Format: "{author} submitted a pull request from {from_branch} to {to_branch} on {timestamp}"
   - Example: "Travis submitted a pull request from staging to master on 1st April 2021 - 9:00 AM UTC"

3. **Merge Events**:
   - Format: "{author} merged branch {from_branch} to {to_branch} on {timestamp}"
   - Example: "Travis merged branch dev to master on 2nd April 2021 - 12:00 PM UTC"

## 📂 Project Structure

```
techstax_assignment/
├── app.py                 # Main Flask application
├── templates/
│   └── index.html        # Dashboard UI
├── requirements.txt      # Python dependencies
├── .env.example         # Environment variables example
├── .env                 # Your environment variables (create this)
└── README.md           # This file
```

## 🎯 MongoDB Schema

```json
{
    "_id": "ObjectId",
    "request_id": "string",     // Commit hash or PR ID
    "author": "string",         // GitHub username
    "action": "string",         // PUSH, PULL_REQUEST, or MERGE
    "from_branch": "string",    // Source branch
    "to_branch": "string",      // Target branch
    "timestamp": "string"       // Formatted UTC timestamp
}
```

## 🧪 Testing the System

1. **Push Test**: Make a commit to `action-repo` → Check dashboard
2. **Pull Request Test**: Create a PR in `action-repo` → Check dashboard
3. **Merge Test**: Merge a PR in `action-repo` → Check dashboard

## 🌐 Deployment Options

### Local Testing with ngrok:
```bash
# Install ngrok and expose your local server
ngrok http 5000
# Use the ngrok URL as your webhook endpoint
```

### Production Deployment:
- Deploy to Heroku, AWS, or any cloud platform
- Update GitHub webhook URL to your production domain

## 🛡️ Security Features

- Optional webhook signature verification
- Input validation and error handling
- CORS protection
- Environment variable configuration

## 🎨 UI Features

- **Real-time Updates**: Auto-refresh every 15 seconds
- **Responsive Design**: Works on desktop and mobile
- **Event Type Indicators**: Color-coded event types
- **Live Status**: Visual indicator of connection status
- **Error Handling**: Graceful error display

## 📱 Dashboard Features

- 🔄 Auto-refresh every 15 seconds
- 📊 Real-time event display
- 🎨 Beautiful, modern UI
- 📱 Mobile responsive
- ⚡ Live status indicator
- 🔍 Event type filtering (visual)

## 🚨 Troubleshooting

### Common Issues:

1. **MongoDB Connection**: Ensure your connection string is correct
2. **Webhook Not Triggered**: Check GitHub webhook settings and URL
3. **CORS Issues**: Ensure proper headers are set
4. **Environment Variables**: Make sure `.env` file is created and loaded

### Debug Mode:
The application runs in debug mode by default. Check console logs for detailed error information.

## 🏆 Assignment Completion

This project fulfills all requirements:

- ✅ GitHub webhook integration
- ✅ Flask application with proper endpoints
- ✅ MongoDB data storage
- ✅ Real-time UI with 15-second polling
- ✅ Support for Push, Pull Request, and Merge events
- ✅ Proper timestamp formatting
- ✅ Clean, modern UI design
- ✅ Error handling and security features

## 📄 License

This project is created for the TechStax Developer Assessment Task.

## 👨‍💻 Author

Created with ❤️ for the TechStax assignment - showcasing Flask, MongoDB, and real-time web development skills!
Flask application to receive and process GitHub webhooks
>>>>>>> 3228341d37b13c975394a3cf93ef5b1b6037c139
