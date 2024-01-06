# Use an Alpine Linux base image
FROM alpine:latest

# Install NSD
RUN apk add --no-cache nsd

# Copy NSD configuration files
COPY nsd.conf /etc/nsd/nsd.conf
COPY zonefile.zone /var/nsd/zones/zonefile.zone

# Expose DNS service ports
EXPOSE 53/udp 53/tcp
EXPOSE 1053/udp 1053/tcp

# Set the entrypoint to NSD
ENTRYPOINT ["nsd", "-d"]
