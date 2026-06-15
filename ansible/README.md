# Ansible Learning Environment

This directory contains a simple Docker-based environment to learn and practice Ansible.

## Architecture

- **Control Node (`ansible-control`)**: The machine where Ansible is installed. You will run your playbooks from here.
- **Managed Node (`ansible-managed`)**: A remote machine (simulated as a Docker container) that Ansible will manage via SSH.

## Prerequisites

- Docker and Docker Compose installed on your host machine.

## Getting Started

1. **Start the environment**:
   ```bash
   docker compose -f docker-compose.ansible.yml up -d
   ```
   *Note: If you encounter "Too Many Requests" from Docker Hub (429 error), you might need to wait for the rate limit to reset or log in to your Docker account using `docker login`.*

2. **Verify the containers are running**:
   ```bash
   docker compose -f docker-compose.ansible.yml ps
   ```

3. **Access the Control Node**:
   ```bash
   docker exec -it ansible-control bash
   ```

4. **Run your first Ansible command**:
   Inside the control node, try pinging the managed node:
   ```bash
   ansible managed -m ping
   ```

5. **Run the provided playbooks**:
   ```bash
   ansible-playbook ping.yml
   ansible-playbook system_info.yml
   ```

## Included Files

- `inventory.ini`: Defines the managed nodes and their connection details.
- `ansible.cfg`: Configuration file for Ansible defaults.
- `ping.yml`: A simple playbook to test connectivity.
- `system_info.yml`: A playbook that demonstrates gathering and displaying system information.
- `control/Dockerfile`: Defines the Ansible control node.
- `managed/Dockerfile`: Defines the managed node with SSH enabled.
