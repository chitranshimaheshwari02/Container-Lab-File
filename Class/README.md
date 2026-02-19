### Docker Networking Practical

This project demonstrates how to:

- View Docker networks
- Create custom bridge networks
- Run containers inside networks
- Test container-to-container communication
- Expose containers to the host machine
- Connect and disconnect containers from networks
- Remove networks

### View Existing Networks

List all available Docker networks:
docker network ls

### Inspect the default bridge network:
docker network create my_app_net

### Create a Custom Bridge Network
docker network create my_app_net

### Verify the network:
docker network ls

### Run Containers in Custom Network
docker run -d --name web --network my_app_net nginx

### Test Container Communication
docker exec utils ping web

### Stop ping with:
Ctrl + C
