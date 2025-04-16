# Task4-demo
nfrastructure as Code (IaC) with Terraform

Project Structure:-
terraform-docker-nginx/
│
├── main.tf


---

main.tf

terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.2"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx_container" {
  name  = "nginx_terraform"
  image = docker_image.nginx.latest
  ports {
    internal = 80
    external = 8080
  }
}

Steps to Execute:-
1. Initialize Terraform:
terraform init

2. Preview the execution plan:
terraform plan

3. Apply the configuration:
terraform apply -auto-approve

4. Check running containers:
docker ps

5. Visit: http://localhost:8080 — You should see the nginx welcome page.

Execution Logs (Sample)

$ terraform init
Initializing the backend...
Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "~> 3.0.2"...
- Installing kreuzwerker/docker v3.0.2...
Terraform has been successfully initialized!

$ terraform apply -auto-approve
docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 3s
docker_container.nginx_container: Creating...
docker_container.nginx_container: Creation complete after 1s

Apply complete! Resources: 2 added, 0 changed, 0 destroyed

updated project structure:-
terraform-docker-nginx/
├── main.tf
├── Dockerfile
└── html/
    └── index.html

html :-
<!DOCTYPE html>
<html>
<head><title>Hello from Terraform Docker!</title></head>
<body>
  <h1>This is a custom page served by Nginx in a Terraform-provisioned Docker container.</h1>
</body>
</html>

Dockerfile:-
FROM nginx:latest
COPY ./html /usr/share/nginx/html

Updated main.tf:-
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.2"
    }
  }
}

provider "docker" {}

resource "docker_image" "custom_nginx" {
  name         = "custom_nginx_image"
  build {
    context = "${path.module}"
    dockerfile = "Dockerfile"
  }
}

resource "docker_container" "nginx_container" {
  name  = "custom_nginx_container"
  image = docker_image.custom_nginx.latest

  env = [
    "NGINX_PORT=80"
  ]

  ports {
    internal = 80
    external = 8080
  }

  volumes {
    host_path      = "${path.module}/html"
    container_path = "/usr/share/nginx/html"
  }
}

Steps to Execute:-
1.open terminal in project folder 
2.Run
terraform init
terraform apply -auto-approve
3.Vist http://localhost:8080 to see custom page.
