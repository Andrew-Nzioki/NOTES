
---

### Managing Docker Images and Using Docker Swarm

This tutorial will guide you through building Docker images, pushing them to Docker Hub, updating your Docker Compose file, and managing your application in a Docker Swarm environment. Before you begin, ensure you have your Docker Hub username and password handy.

---

#### Step 1: Build Your Images Manually

1. **Frontend Image**
   Ensure you are in the root project directory, then navigate to the frontend directory and build the image:

   ```bash
   cd frontend
   docker build -t username/front-end:latest .
   ```

2. **Backend Image**
   Navigate to the API directory from the root project directory and build the backend image:

   ```bash
   cd api
   docker build -t username/backend:latest .
   ```

#### Step 2: Push the Images to Docker Hub

With the images built, the next step is to push them to Docker Hub so that they are accessible to all nodes in your Docker Swarm (assuming a multi-node setup):

1. **Login to Docker Hub**

   ```bash
   docker login
   ```

2. **Push Frontend Image**

   ```bash
   docker push username/front-end:latest
   ```

3. **Push Backend Image**

   ```bash
   docker push username/backend:latest
   ```

#### Step 3: Update Your Docker Compose File

Update your `docker-compose.yml` file to reference the Docker Hub images instead of building from the local context:

```yaml
version: "3.8"

services:
  front-end:
    image: username/front-end:latest
    ports:
      - "5173:80"
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure

  backend:
    image: username/backend:latest
    ports:
      - "8000:8000"
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure

networks:
  default:
    driver: overlay
```

#### Step 4: Deploy the Stack Again

Deploy your stack to the swarm with the updated Docker Compose file:

```bash
docker stack deploy -c docker-compose.yml sample-name
```

### Post-Deployment Management

After deploying your application stack, here are several practices to follow for effective management, monitoring, and maintenance:

1. **Verify the Deployment**

   Check the deployment status:

   ```bash
   docker stack services sample-name
   ```

2. **Inspect Individual Services**

   For detailed service information and logs:

   ```bash
   docker service inspect --pretty <service_name>
   docker service logs <service_name>
   ```

3. **Scale Services**

   Adjust the number of service instances as needed:

   ```bash
   docker service scale <service_name>=<num_replicas>
   ```

4. **Update Services**

   Update service images or configurations:

   ```bash
   docker pull username/backend:latest
   docker service update --image username/backend:latest backend
   ```

5. **Monitor Swarm Health**

   Monitor the health and status of the swarm:

   ```bash
   docker node ls
   docker system events --filter type=service
   ```

### Removing the Docker Stack

When it's time to stop and clean up:

1. **Remove the Docker Stack**

   ```bash
   docker stack rm sample-name
   ```

2. **Verify That the Services Have Stopped**

   ```bash
   docker service ls
   ```

3. **Clean Up Any Remaining Resources**

   Clean up unused Docker resources, including networks and optionally volumes:

   ```bash
   docker system prune
   docker system prune --volumes  # Use cautiously
   ```

4. **Confirm Swarm Node Status**

   Check if your node should leave the swarm:

   ```bash
   docker node ls
   docker swarm leave --force  # For the last or only node
   docker swarm leave  # For a worker node
   ```