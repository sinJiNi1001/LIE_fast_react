🚀 Lead Intelligence Engine: Windows Server Deployment Guide
This guide outlines the complete, step-by-step process for deploying the Lead Intelligence Engine onto a Windows Server using Nginx.

The application uses a decoupled architecture: the React frontend is served as static HTML/JS, while the FastAPI backend runs as a persistent Windows Service.

🏗️ Deployment Architecture
Plaintext
[ Internet (Port 80/443) ] ---> [ Nginx Reverse Proxy ]
                                      │
       ┌──────────────────────────────┴──────────────────────────────┐
       ▼ (/ route)                                                   ▼ (/api/ route)
[ React Static Assets ]                                    [ FastAPI Background Service ]
C:/inetpub/wwwroot/lie_frontend                            Custom Internal Port (e.g., 9000)
📋 0. Server Prerequisites
Ensure your Windows Server has the following installed:

Python 3.10+ (Added to System PATH during installation)

Node.js & npm (To compile the React frontend)

Nginx for Windows (Configured as your web server)

NSSM (Non-Sucking Service Manager) (To daemonize the Python backend)

📥 1. Transfer and Extract the Source Code
Instead of pulling from a repository, you will manually move the codebase to the server.

Transfer your project ZIP file (e.g., LIE_fast_react.zip) to the Windows Server.

Right-click the ZIP file and select Extract All... to a permanent location on your server (for example: C:\Apps\LIE_fast_react).

Open a Command Prompt and navigate into your newly extracted folder:

DOS
cd C:\Apps\LIE_fast_react
🎨 2. Build the React Frontend
The React application must be compiled into optimized static assets before Nginx can serve it.

In your command prompt, navigate to the frontend directory:

DOS
cd frontend
Install dependencies and compile the production bundle:

DOS
npm install
npm run build
A new dist/ folder will be generated. Copy the entire contents of this dist/ folder to your permanent web serving directory.
(Example: C:\inetpub\wwwroot\lie_frontend)

🎛️ 3. The Custom Port Rule (READ CAREFULLY)
Because Nginx handles the public traffic, the FastAPI backend needs to run on a hidden, internal port.

Choose a 4-digit port number right now (e.g., 9000, 8080, 7777). You will manually type this number in exactly two places during the next steps. Do not change any code in the repository; the port is strictly handled by the server configuration.

🐍 4. Setup and Daemonize the FastAPI Backend
We will wrap the Python backend into a native Windows Service using NSSM so it runs perpetually in the background.

Open a command prompt and navigate to the backend directory of your extracted folder:

DOS
cd C:\Apps\LIE_fast_react\backend
Create a virtual environment and install the Python dependencies:

DOS
python -m venv venv
.\venv\Scripts\activate
pip install -r requirements.txt
Create a .env file inside the backend/ folder and add your production API keys:


GROQ_API_KEY=your_groq_api_key_here
SERPER_API_KEY=your_serper_api_key_here
FIRECRAWL_API_KEY=your_firecrawl_api_key_here

Open an Administrator Command Prompt and create the background service:

DOS
nssm install LIE_Backend
[PORT REPLACEMENT #1]: The NSSM GUI will open. Configure it exactly as follows (adjusting the paths if you extracted your ZIP somewhere else):

Path: C:\Apps\LIE_fast_react\backend\venv\Scripts\python.exe

Startup Directory: C:\Apps\LIE_fast_react\backend

Arguments: -m uvicorn main:app --host 127.0.0.1 --port 9000 (<-- Type your chosen port here!)

Go to the Details tab, set startup type to Automatic, click Install Service.

Start the backend:

DOS
nssm start LIE_Backend
🌐 5. Configure Nginx Routing
Finally, tell Nginx to serve the React files for standard visits, and forward /api/ requests to your running FastAPI service.

Open your Nginx configuration file (usually located at C:\nginx\conf\nginx.conf).

[PORT REPLACEMENT #2]: Update your server block. Look at the proxy_pass line below and replace 9000 with your chosen port:

Nginx
server {
    listen       80;
    server_name  yourdomain.com www.yourdomain.com; # Your server IP or domain

    # Route 1: Serve the React Frontend
    location / {
        root   C:/inetpub/wwwroot/lie_frontend; # The folder from Step 2
        index  index.html;
        try_files $uri $uri/ /index.html; 
    }

    # Route 2: Forward to FastAPI
    location /api/ {
        # <-- Type your chosen port here!
        proxy_pass http://127.0.0.1:9000/api/; 
        
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        proxy_read_timeout 120s;
        proxy_connect_timeout 120s;
    }
}
Open a command prompt and reload Nginx to push the routing live:

DOS
nginx -s reload

🎉 Deployment Complete! The Lead Intelligence Engine is now live on your Windows Server.
