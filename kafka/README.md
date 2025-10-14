# Kafka Docker Compose Setup

A Docker Compose configuration for running Apache Kafka with Zookeeper and Kafbat UI for easy cluster management and monitoring.

## Services

### Zookeeper
- **Image**: confluentinc/cp-zookeeper:7.6.1
- **Port**: 2181
- **Purpose**: Coordinates and manages the Kafka cluster

### Kafka Broker
- **Image**: confluentinc/cp-kafka:7.6.1
- **Ports**:
  - `9092`: External access (use this from your host machine)
  - `29092`: Internal Docker network communication
- **Features**:
  - 7-day log retention (168 hours)
  - Persistent data storage via Docker volume
  - Single broker configuration (replication factor: 1)

### Kafbat UI
- **Image**: ghcr.io/kafbat/kafka-ui:latest
- **Port**: 8080
- **Purpose**: Web-based UI for managing and monitoring Kafka
- **Access**: http://localhost:8080

## Prerequisites

- Docker installed
- Docker Compose installed
- Port availability: 2181, 8080, 9092, 29092

## Getting Started

1. **Start the services**:
   ```bash
   docker-compose up -d
   ```

2. **Check service status**:
   ```bash
   docker-compose ps
   ```

3. **Access Kafbat UI**:
   Open your browser and navigate to http://localhost:8080

4. **View logs**:
   ```bash
   docker-compose logs -f
   ```

## Configuration

### Kafbat UI Configuration
The UI expects a configuration file at `./kui/config.yml`. Create this file to define your Kafka clusters and connection settings.

Example `kui/config.yml`:
```yaml
kafka:
  clusters:
    - name: local
      bootstrapServers: kafka:29092
```

## Connecting to Kafka

### From Host Machine
Use connection string: `localhost:9092`

### From Docker Containers
Use connection string: `kafka:29092`

## Useful Commands

**Stop all services**:
```bash
docker-compose down
```

**Stop and remove volumes** (⚠️ deletes all Kafka data):
```bash
docker-compose down -v
```

**Restart services**:
```bash
docker-compose restart
```

**View Kafka logs**:
```bash
docker-compose logs -f kafka
```

## Data Persistence

Kafka data is persisted in the `kafka_data` Docker volume, ensuring your topics and messages survive container restarts.

## Troubleshooting

- **Services won't start**: Ensure ports 2181, 8080, 9092, and 29092 are not already in use
- **Cannot connect to Kafka**: Verify the broker is running with `docker-compose ps`
- **Kafbat UI not loading**: Check that `./kui/config.yml` exists and is properly formatted
- **Connection refused**: Allow a few seconds for Kafka to fully initialize after starting

## Notes

- This is a single-broker setup suitable for development and testing
- For production, consider using multiple brokers and higher replication factors
- All services are configured with `restart: unless-stopped` for automatic recovery