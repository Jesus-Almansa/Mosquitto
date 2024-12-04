# Mosquitto MQTT Broker with Docker Compose

This project provides a ready-to-use Mosquitto MQTT Broker setup using Docker Compose. It includes configuration for secure connections with authentication and WebSocket support.

## Features

- **Lightweight MQTT Broker**: Based on the official Eclipse Mosquitto image.
- **Authentication**: User-based authentication with a password file.
- **WebSocket Support**: Allows MQTT communication via WebSockets on port `9001`.
- **Persistence**: Retains messages and subscriptions across broker restarts.
- **Resource Limits**: Prevents the container from overusing system resources.

---

## Project Structure

```plaintext
mosquitto_files/
├── config/
│   ├── mosquitto.conf       # Mosquitto configuration file
│   ├── pwfile               # Password file for authentication
├── data/                    # Persistent storage for MQTT data
├── log/                     # Log files for Mosquitto
docker-compose.yml           # Docker Compose configuration
```

---

## Prerequisites

- Docker and Docker Compose installed on your system.
- `mosquitto-clients` for testing (optional).

---

## Setup and Deployment

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/Jesus-Almansa/Mosquitto.git
   cd Mosquitto
   ```

2. **Prepare Configuration Files**:
   - Ensure `mosquitto_files/config/mosquitto.conf` has the following content:
     ```plaintext
     allow_anonymous false
     listener 1883
     listener 9001
     protocol websockets
     persistence true
     password_file /mosquitto/config/pwfile
     persistence_file mosquitto.db
     persistence_location /mosquitto/data
     ```

   - Create the password file and add credentials:
     ```bash
     mosquitto_passwd -c mosquitto_files/config/pwfile <username>
     ```

3. **Set Permissions**:
   ```bash
   chmod 600 mosquitto_files/config/pwfile
   ```

4. **Deploy the Mosquitto Broker**:
   Run the following command to start the container:
   ```bash
   docker compose up --build
   ```

5. **Verify Logs**:
   Check the logs to ensure the broker is running correctly:
   ```bash
   docker logs mosquitto
   ```

---

## Testing

### **Step 1: Subscribe to a Topic**

Run the following command **on the host** to subscribe to a topic (e.g., `test/topic`):
```bash
mosquitto_sub -h localhost -t test/topic -u <username> -P <password>
```

- **Where to Run**: **On the Host**
- **Explanation**:
  - Connects to the broker on `localhost` with the provided username and password.

---

### **Step 2: Publish a Message**

In a **new terminal on the host**, publish a message to the same topic:
```bash
mosquitto_pub -h localhost -t test/topic -m "Hello, MQTT!" -u <username> -P <password>
```

- **Where to Run**: **On the Host**
- **Explanation**:
  - Sends a message to the `test/topic`. The subscriber from Step 1 should receive it.

---

### **Step 3: Subscribe from Inside the Container**

Access the container:
```bash
docker exec -it mosquitto sh
```

Then, subscribe to a topic:
```bash
mosquitto_sub -h localhost -t test/topic -u <username> -P <password>
```

- **Where to Run**: **Inside the Container**
- **Explanation**:
  - Verifies that the broker works within the container.

---

### **Step 4: Publish from Inside the Container**

While inside the container, publish a message:
```bash
mosquitto_pub -h localhost -t test/topic -m "Hello from the container!" -u <username> -P <password>
```

- **Where to Run**: **Inside the Container**
- **Explanation**:
  - Sends a message from within the container.

---

### **Step 5: Test WebSocket Connections**

#### **Option A: Use MQTT Explorer**
1. Download and open [MQTT Explorer](https://mqtt-explorer.com/).
2. Add a new connection:
   - Host: `localhost`
   - Port: `9001`
   - Protocol: WebSocket.
   - Username and Password: Credentials from the `pwfile`.

3. Connect to the broker, subscribe to `test/topic`, and publish messages.

- **Where to Run**: **On the Host**

---

### **Step 6: Test Persistence**

1. **Publish a Retained Message** **on the host**:
   ```bash
   mosquitto_pub -h localhost -t retained/topic -m "Retained message" -r -u <username> -P <password>
   ```

2. **Stop the Broker**:
   ```bash
   docker compose down
   ```

3. **Restart the Broker**:
   ```bash
   docker compose up
   ```

4. **Verify Retained Message**:
   ```bash
   mosquitto_sub -h localhost -t retained/topic -u <username> -P <password>
   ```

- **Where to Run**: All commands in this step are run **on the host**.

---

### **Step 7: Check Logs**

Inspect the logs for issues:
```bash
docker logs mosquitto
```

- **Where to Run**: **On the Host**
- **Explanation**:
  - Verifies if there are any errors or warnings related to configuration or MQTT clients.

---

## Ports

- **1883**: Standard MQTT protocol.
- **9001**: WebSocket protocol.

---

## Troubleshooting

- Ensure all paths in `docker-compose.yml` exist on your host machine.
- Check container logs for errors:
  ```bash
  docker logs mosquitto
  ```
- If permissions issues arise, ensure the `pwfile` is readable by the container:
  ```bash
  chmod 600 mosquitto_files/config/pwfile
  ```

