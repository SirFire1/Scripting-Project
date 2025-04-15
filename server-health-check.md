#Bash Script
```bash
#!/bin/bash

# Server Health Investigation Script
# Installs packages, checks resources, and verifies services

# Colors for output
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m' # No Color

# Function to install packages
install_packages() {
    echo -e "\n${YELLOW}Installing required packages...${NC}"

    # Update package lists
    sudo apt-get update

    # Install Apache and Redis
    sudo apt-get install -y apache2 redis-server

    echo -e "${GREEN}Package installation complete.${NC}"
}

# Function to check system resources
check_resources() {
    echo -e "\n${YELLOW}=== System Resources ===${NC}"

    # Disk space
    echo -e "\n${GREEN}Disk Space:${NC}"
    df -h --output=source,size,pcent,avail | grep -E '^(/dev|[A-Z]:)'

    # Memory
    echo -e "\n${GREEN}Memory:${NC}"
    free -h

    # CPU
    echo -e "\n${GREEN}CPU:${NC}"
    echo "vCPUs: $(nproc)"
    echo "Load average: $(uptime | awk -F'load average: ' '{print $2}')"
}

# Function to check service status
check_services() {
    echo -e "\n${YELLOW}=== Service Status ===${NC}"

    # Apache status
    echo -e "\n${GREEN}Apache:${NC}"
    if systemctl is-active --quiet apache2; then
        echo -e "Status: ${GREEN}active${NC}"
    else
        echo -e "Status: ${RED}inactive${NC}"
    fi
    sudo systemctl status apache2 --no-pager | head -5

    # Redis status
    echo -e "\n${GREEN}Redis:${NC}"
    if systemctl is-active --quiet redis-server; then
        echo -e "Status: ${GREEN}active${NC}"
    else
        echo -e "Status: ${RED}inactive${NC}"
    fi
    sudo systemctl status redis-server --no-pager | head -5
}

# Main function
main() {
    echo -e "${GREEN}=== Server Health Investigation ===${NC}"

    # Install packages
    install_packages

    # Check system resources
    check_resources

    # Check service status
    check_services

    echo -e "\n${GREEN}=== Investigation Complete ===${NC}"
}

# Run main function
main
