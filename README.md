1. Define a multi-container app

Docker Compose — compose.yaml:
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:

yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: yourusername/my-app:latest
          ports:
            - containerPort: 3000

2. Wire service-to-service networking

in js
const dbHost = process.env.DB_HOST || "db";  // "db" matches the service name above

3. Add health checks and restart policies
services:
  app:
    build: .
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 10s
      timeout: 3s
      retries: 3
    restart: unless-stopped

4. Declare desired replicas and test self-recovery (kill one)
services:
  app:
    deploy:
      replicas: 2

bash
 Docker Compose
docker compose ps
docker kill <container_id>
docker compose ps    # with restart: unless-stopped, it should come back

5. Inject config/secrets at runtime
services:
  app:
    env_file:
      - .env
Then reference it in the deployment:
envFrom:
  - secretRef:
      name: app-secrets
<img width="1536" height="1024" alt="ss885" src="https://github.com/user-attachments/assets/9a5ed8c1-ba22-4e5a-8ce5-60a2a4e8f17b" />

    
