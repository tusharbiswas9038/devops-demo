# Use the official PostgreSQL image as base
FROM postgres:14

# Copy your SQL init script into the container
COPY init.sql /docker-entrypoint-initdb.d/init.sql

# Optional: Ensure correct permissions (especially on Linux)
RUN chmod 755 /docker-entrypoint-initdb.d/init.sql
