# **Local Development Environment**

This repository provides a local development environment for testing microservices with **LocalStack** and **Docker Compose**. It simulates AWS services like SQS, allowing you to develop and test your services locally before deploying them to production.

## **Services**

The following services are configured in this environment:

1. **LocalStack** – Simulates AWS services (e.g., SQS).
2. **Action Builder Service** – A microservice that sends messages to SQS.
3. **Notification Service** – A microservice that processes messages from SQS and simulates sending notifications.

## **Prerequisites**

Ensure you have the following installed:

- **Docker**: [Get Docker](https://docs.docker.com/get-docker/)
- **Docker Compose**: [Install Docker Compose](https://docs.docker.com/compose/install/)
- **AWS CLI**: (Optional) for interacting with LocalStack. [Install AWS CLI](https://aws.amazon.com/cli/)

## **Setup**

1. **Clone the Repository**:

   ```bash
   git clone https://github.com/your-org/local-dev-environment.git
   cd local-dev-environment
   ```

2. **Build and Start the Services**:

   Run the following command to build and start all services (LocalStack, Action Builder Service, Notification Service):

   ```bash
   docker-compose up --build -d
   aws --endpoint-url=http://localhost:4566 sqs create-queue --queue-name NotificationQueue

   ```

   This will:

   - Start **LocalStack** to simulate AWS services.
   - Start the **Action Builder Service** and **Notification Service** containers.

3. **Verify Services Are Running**:

   You can verify the running containers:

   ```bash
   docker-compose ps
   ```

   You should see the containers for `localstack`, `action-builder-service`, and `notification-service` running.

## **Usage**

### **Sending a Message to SQS via Action Builder Service**

You can send a test message to the SQS queue via the **Action Builder Service**:

1. Make a `POST` request to the **Action Builder Service** (running on port 3000) using `curl`:

   ```bash
   curl -X POST http://localhost:3000/action \
     -H "Content-Type: application/json" \
     -d '{"details": "NFT Sold"}'
   ```

   This will trigger the **Action Builder Service** to send a message to the SQS queue.

### **Verifying Messages in SQS**

To check if the message was received by SQS in LocalStack, you can use the AWS CLI:

```bash
aws --endpoint-url=http://localhost:4566 sqs receive-message \
  --queue-url http://localhost:4566/000000000000/NotificationQueue
```

### **Processing Messages via Notification Service**

The **Notification Service** will continuously poll the SQS queue and process incoming messages. Check the logs of the Notification Service to ensure it's receiving and processing messages:

```bash
docker-compose logs notification-service
```

## **Managing the Environment**

### **Stop Services**

To stop all services, run:

```bash
docker-compose down
```

### **Rebuild the Environment**

If you make changes to any of the microservices, you’ll need to rebuild the environment:

```bash
docker-compose up --build
```

## **Testing Locally**

1. **Send a Test Message**: Use the **Action Builder Service** to send a message to SQS.
2. **Verify the Message in SQS**: Use AWS CLI to check for messages in LocalStack's SQS.
3. **Check the Notification Service**: Ensure the Notification Service processes the message correctly by inspecting its logs.

## **Troubleshooting**

- **Empty Response from Server**: Ensure the correct ports are exposed and services are running.
- **No Messages in SQS**: Check that the Action Builder Service is sending messages to the correct SQS queue (verify queue URL and region).

## **Environment Variables**

Make sure the following environment variables are correctly set for your services:

- **AWS_REGION**: The region for LocalStack, e.g., `eu-central-1`.
- **QUEUE_URL**: The LocalStack SQS queue URL, e.g., `http://localhost:4566/000000000000/NotificationQueue`.

## **Directory Structure**

```
local-dev-environment/
├── docker-compose.yml    # Docker Compose configuration
├── action-builder-service/
│   └── Dockerfile        # Dockerfile for the Action Builder Service
├── notification-service/
│   └── Dockerfile        # Dockerfile for the Notification Service
└── localstack/
    └── Dockerfile        # Dockerfile for LocalStack (if customized)
```
