

Just for giggles, I wanted to see if I could use Terraform to create and manage my docker containers and per usual, bumped into some issues when running the example code from the Hashicorp tutorial.

'''HCL
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0.1"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id
  name  = "tutorial"

  ports {
    internal = 80
    external = 8000
  }
}
'''
The issue was with the provider config.

On macOS, access to Docker’s internal sockets (at least in my environment) is located within the Docker Desktop VM, so changing the provider to point to my home directory fixed the issue.

'''HCL
provider "docker" {
  host = "unix://${pathexpand("~")}/.docker/run/docker.sock"
}
'''
Optionally, you could use an environment variable.

If you’re attempting the same for some reason, I hope this will save you some back and forth time with your favorite LLM.

