# Use an Alpine Linux base image
FROM alpine:latest

# Install Unbound and bind-tools
RUN apk add --no-cache unbound bind-tools

# Copy Unbound configuration files
COPY unbound.conf /etc/unbound/unbound.conf

# Expose DNS service ports
EXPOSE 53/udp 53/tcp

# Set the entrypoint to Unbound
ENTRYPOINT ["unbound", "-d"]
