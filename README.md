# Full-Stack Task Manager with CI/CD

Learning project using React, Node.js, Express, MongoDB, Docker Compose, Jenkins, GitHub and AWS EC2.

## Architecture
- React frontend built with Vite and served by Nginx
- Nginx proxies `/api` requests to the Node.js REST API
- Express API stores tasks in MongoDB
- Docker Compose runs frontend, backend and database
- Jenkins tests, builds and deploys to a separate application EC2

## API endpoints
- `GET /api/health`
- `GET /api/tasks`
- `POST /api/tasks` with JSON `{"title":"Example task"}`
- `PATCH /api/tasks/:id` with JSON `{"completed":true}`

## Run locally
```bash
docker compose up -d --build
```
Open `http://localhost`.

## Stop containers
```bash
docker compose down
```
MongoDB data remains in `mongo_data`. `docker compose down -v` deletes the data volume.

## Jenkins setup
1. Push this repository to GitHub.
2. Configure a Pipeline job using Pipeline script from SCM, script path `Jenkinsfile`.
3. Install/configure Pipeline, Git, and SSH Agent plugins.
4. Add Jenkins credential `app-ec2-key` as an SSH private-key credential with username `ubuntu`.
5. Replace `YOUR_GITHUB_USERNAME` in `Jenkinsfile` with your GitHub username.
6. Set `APP_HOST` to the application EC2 public IP.
7. Jenkins agent needs Git, Node.js/npm, Docker, SSH and network access to app EC2.
8. App EC2 needs Docker Compose, repository access, and SSH inbound restricted to Jenkins.

## AWS security
- Allow HTTP port 80 as required; restrict SSH port 22 to your IP/Jenkins host.
- Do not expose MongoDB 27017 or API 5000 publicly.
- Never commit private keys, AWS access keys, or `.env` files.
- For production, add authentication, TLS, secrets management, verified SSH host keys, monitoring and backups.

## Terraform
The `terraform/` directory is a placeholder for the next phase; provisioning is not wired into this pipeline yet.
