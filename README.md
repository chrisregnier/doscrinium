# Doscrinium
A document storage cabinet (scrinium is Latin for cabinet). This is a dropbox like clone to test out new things.

## Running

Everything is setup for local development in `docker-compose.yml`.

App data, and storage items will be stored in `~/apps/doscrinium/`.
```bash
mkdir -p ~/apps/doscinium/db
mkdir -p ~/apps/doscinium/storage
```

Start the docker stack and connect to http://localhost:3000. The backend is accessible on 8080 and the debug port is 8000 (see [here](https://docs.docker.com/language/java/develop/#connect-a-debugger) for more details).

```bash
docker-compose up
```
