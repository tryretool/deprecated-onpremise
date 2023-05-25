### Docker cheatsheet

Below is a cheatsheet for useful Docker commands. Note that you may need to prefix them with `sudo`.

| Command                                                                                         | Description                                                                                                                   |
| ----------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `docker-compose up -d`                                                                          | Builds, (re)creates, starts, and attaches to containers for a service. `-d`allows containers to run in background (detached). |
| `docker-compose down`                                                                           | Stops and remove containers and networks                                                                                      |
| `docker-compose stop`                                                                           | Stops containers, but does not remove them and their networks                                                                 |
| `docker ps -a`                                                                                  | Display all Docker containers                                                                                                 |
| `docker-compose ps -a`                                                                          | Display all containers related to images declared in the `docker-compose` file.                                               |
| `docker logs -f <container_name>`                                                               | Stream container logs to stdout                                                                                               |
| `docker exec -it <container_name> psql -U <postgres_user> -W <postgres_password> <postgres_db>` | Runs `psql` inside a container                                                                                                |
| `docker kill $(docker ps -q)`                                                                   | Kills all running containers                                                                                                  |
| `docker rm $(docker ps -a -q)`                                                                  | Removes all containers and networks                                                                                           |
| `docker rmi -f $(docker images -q)`                                                             | Removes (and un-tags) all images from the host                                                                                |
| `docker volume rm $(docker volume ls -q)`                                                       | Removes all volumes and completely wipes any persisted data                                                                   |
