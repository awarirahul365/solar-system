FROM ubuntu:20.04

# Install any dependencies
RUN apt-get update && apt-get install -y \
    curl \
    jq

# Copy entrypoint script
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Set the entrypoint for the Docker container
ENTRYPOINT ["/entrypoint.sh"]
