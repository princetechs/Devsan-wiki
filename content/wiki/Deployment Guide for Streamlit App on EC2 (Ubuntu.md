Here’s a short deployment guide you can add to your README or deployment documentation:

**Deployment Guide for Streamlit App on EC2 (Ubuntu)**

  

**1. Set Up EC2 Instance**

• Launch an EC2 instance with Ubuntu.

• Ensure that **port 80** is open in the **Inbound Rules** of your EC2 Security Group.

  

**2. Install Dependencies**

  

SSH into your EC2 instance and run the following:

```
# Update and install required packages
sudo apt update
sudo apt install python3-pip python3-venv

# Install tmux for managing sessions (optional)
sudo apt install tmux
```

**3. Clone the Project**

  

Clone your Streamlit app repository:

```
git clone <repository_url>
cd <project_folder>
```

**4. Set Up Virtual Environment (Recommended)**

  

Create and activate a virtual environment:

```
# Create virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate

# Install project dependencies
pip install -r requirements.txt
```

**5. Run the App**

  

To run the app on port 80 (accessible via HTTP):

```
# Run Streamlit with nohup to keep it alive after SSH disconnect
nohup streamlit run app.py --server.port 80 &
```

Alternatively, for better session management, use tmux:

```
tmux
streamlit run app.py --server.port 80
# Press Ctrl+B, then D to detach from the tmux session
```

**6. Access the App**

  

You can now access the Streamlit app at http://<EC2_PUBLIC_IP>. Make sure port 80 is open in the EC2 Security Group.

  

**7. Stop the App (If Needed)**

  

To stop the app:

1. Find the process ID (PID):

```
ps aux | grep streamlit
```

  

2. Kill the process:

```
kill <PID>
```

Or force kill if it doesn’t stop:

```
kill -9 <PID>
```

This guide should cover your basic deployment steps! Let me know if you need any modifications or additional steps.