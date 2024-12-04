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
   git clone https://github.com/<your-username>/<your-repo-name>.git
   cd <your-repo-name>
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

### Using `mosquitto_sub` and `mosquitto_pub`

- **Subscribe to a Topic**:
  ```bash
  mosquitto_sub -h localhost -t test/topic -u <username> -P <password>
  ```

- **Publish a Message**:
  ```bash
  mosquitto_pub -h localhost -t test/topic -m "Hello, MQTT!" -u <username> -P <password>
  ```

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