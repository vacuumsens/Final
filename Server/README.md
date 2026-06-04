# Senseeker Server Module

## Overview

The Server module is the central hub of the Senseeker system. It provides a web-based dashboard for monitoring client devices, managing data collection, configuring system settings, and storing sensor data in MongoDB. The Server consists of a Frontend UI and a Backend API with database integration.

##
Just install using windows installer Server.exe on computer on local network and run app and should be good to go. 
If you want to work with the files and scripts continuie below. 

## Architecture

The Server module has two main components:

1. **Frontend**: Web-based user interface (HTML, CSS, JavaScript)
2. **Backend**: Node.js/Express API server with MongoDB database integration

## System Requirements

- **Node.js** (v14 or higher recommended)
- **npm** (Node Package Manager)
- **MongoDB** (local or remote instance)
- **Port availability**: 
  - Frontend: 3000 (default)
  - Backend API: 5000 (or configured port)
- **Administrator privileges** for port binding

## Installation

### 1. Extract Source Code

```bash
unzip Server/Codebase.zip
cd server-directory
```

### 2. Install Dependencies

```bash
npm install
```

This command reads `package.json` and installs all required Node modules.

## Running the Server

### Development Mode

From the project root directory:

```bash
npm start
```

### External/Host Mode

To run the server for external access:

```bash
npm run host
```

This exposes the server to external network connections.

### Checking Installation

After starting the server, verify it's running:

```bash
# Check if process is listening on ports
lsof -i :3000
lsof -i :5000
```

## Functionalities

### Frontend

- **Dashboard**: Real-time monitoring of all connected Client devices
- **Data Visualization**: Charts and graphs for sensor data analysis
- **Settings Panel**: Configure system parameters and thresholds
- **Client Management**: View device status and history
- **Alerts**: Visual notifications for system events

### Backend

- **API Endpoints**: RESTful API for data transmission and device management
- **MongoDB Integration**: Persistent storage of sensor data and configurations
- **Authentication**: Optional password protection (can be enabled/disabled in settings)
- **Port Configuration**: Flexible port management for different deployment scenarios
- **Device Communication**: Handles requests from Client modules

### Database

- **MongoDB Storage**: Stores sensor readings, device configurations, and user settings
- **Password Protection**: Optional security layer for data access
- **Data Retention**: Configurable data retention policies
- **Backup Support**: Compatible with standard MongoDB backup procedures

## Configuration

### Database Setup

#### Option 1: Local MongoDB

```bash
# Install MongoDB (if not already installed)
# macOS with Homebrew:
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB
brew services start mongodb-community

# Verify connection
mongo
```

#### Option 2: Remote MongoDB (e.g., MongoDB Atlas)

1. Create a MongoDB Atlas account at https://www.mongodb.com/cloud/atlas
2. Create a cluster and database
3. Update connection string in backend configuration:
   ```
   MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/dbname
   ```

### Environment Variables

Create a `.env` file in the backend directory:

```bash
PORT=5000
MONGODB_URI=mongodb://localhost:27017/senseeker
NODE_ENV=production
FRONTEND_URL=http://localhost:3000
```

### Password Protection

Enable or disable password protection via the web settings:

1. Access the Server dashboard
2. Navigate to **Settings**
3. Toggle **Password Protection** option
4. Set credentials as needed

## Troubleshooting

### Port Connection Issues

**Problem**: "Address already in use" or dashboard appears empty

**Solution**:
1. Check if ports are already in use:
   ```bash
   lsof -i :3000
   lsof -i :5000
   ```
2. Kill existing processes (if safe):
   ```bash
   kill -9 <PID>
   ```
3. Allow permissions for port binding:
   ```bash
   # On Linux, grant permission to bind low ports
   sudo setcap 'cap_net_bind_service=+ep' /usr/bin/node
   ```
4. Restart the server:
   ```bash
   npm start
   ```

### Dashboard Empty

**Problem**: Dashboard shows no data or devices

**Solution**:
1. Verify permissions are correctly set (especially for port binding)
2. Check MongoDB connection:
   ```bash
   mongo
   use senseeker
   db.devices.find()
   ```
3. Ensure Client devices are configured to send data to correct Server address
4. Check backend logs for connection errors:
   ```bash
   npm start
   ```

### MongoDB Connection Failed

**Problem**: Cannot connect to MongoDB

**Solution**:
1. Verify MongoDB is running:
   ```bash
   # macOS
   brew services list
   
   # Linux
   sudo systemctl status mongod
   ```
2. Check connection string in `.env`
3. Verify network connectivity to MongoDB instance
4. Check MongoDB authentication credentials

### Performance Issues

**Problem**: Slow data loading or UI lag

**Solution**:
1. Monitor MongoDB performance:
   ```bash
   mongo
   > db.serverStatus()
   ```
2. Implement data pagination in API responses
3. Add database indexes on frequently queried fields
4. Clear old data if storage is full

## File Structure

```
Server/
├── Codebase.zip          # Complete source code
├── Server.exe            # Standalone Windows executable
├── Frontend/             # Web UI (extracted from zip)
│   ├── index.html
│   ├── css/
│   └── js/
├── Backend/              # Node.js API (extracted from zip)
│   ├── server.js
│   ├── routes/
│   ├── models/
│   ├── config/
│   ├── package.json
│   └── .env
└── README.md             # This file
```

## Deployment Options

### Option 1: Development Environment

```bash
npm install
npm start
```

### Option 2: Production Environment (External Access)

```bash
npm install
npm run host
```

### Option 3: Windows Standalone

Run the provided `Server.exe` file:

```bash
./Server.exe
```

(Alternatively, double-click the executable)

### Option 4: Docker (Optional)

For containerized deployment:

```bash
docker build -t senseeker-server .
docker run -p 3000:3000 -p 5000:5000 senseeker-server
```

## API Documentation

### Core Endpoints

- **GET** `/api/devices` - Retrieve all connected devices
- **POST** `/api/data` - Submit sensor data from Client
- **GET** `/api/data/:deviceId` - Retrieve historical data for a device
- **PUT** `/api/config/:deviceId` - Update device configuration
- **GET** `/api/settings` - Retrieve server settings
- **PUT** `/api/settings` - Update server settings

For detailed API documentation, refer to the Backend API specifications within the extracted codebase.

## Security Considerations

- **Password Protection**: Enable authentication for sensitive deployments
- **Database Credentials**: Store safely in environment variables
- **Network Security**: Use HTTPS in production environments
- **API Rate Limiting**: Implement to prevent abuse
- **Data Validation**: Sanitize all incoming data

## Performance Optimization

- **Database Indexing**: Create indexes on frequently queried fields
- **Caching**: Implement caching for static assets
- **Data Pagination**: Limit data returned per API call
- **Compression**: Enable gzip compression for API responses
- **Load Balancing**: Consider clustering for high-traffic deployments

## Monitoring & Maintenance

### Health Check

```bash
# Verify all services are running
curl http://localhost:3000/health
curl http://localhost:5000/api/health
```

### Log Monitoring

```bash
# View real-time logs
npm start  # Logs displayed in console

# Or check application logs
tail -f logs/server.log
```

### Database Maintenance

```bash
# Backup MongoDB
mongodump --out /backup/location

# Restore MongoDB
mongorestore /backup/location
```

## Advanced Configuration

### Custom Ports

Edit the backend configuration to use different ports:

```javascript
// In backend/server.js or .env
PORT=8000  // Change from default 5000
FRONTEND_PORT=8080  // Change from default 3000
```

### CORS Configuration

Allow specific client origins:

```javascript
app.use(cors({
  origin: ['http://localhost:3000', 'http://device-ip:3000'],
  credentials: true
}));
```

### SSL/TLS Support

For production deployments with HTTPS:

```javascript
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('path/to/key.pem'),
  cert: fs.readFileSync('path/to/cert.pem')
};

https.createServer(options, app).listen(443);
```

## Support & Documentation

For additional information:
- Check `PossibleChangesNotes.txt` for known issues and recommendations
- Review the Client module's README for device integration details
- Consult MongoDB documentation for advanced database operations

## License

Part of the Senseeker Deliverables project.

---

**Last Updated**: June 2026
