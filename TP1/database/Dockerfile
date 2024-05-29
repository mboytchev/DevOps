# Use the PostgreSQL 14.1 image based on Alpine as the base image
FROM postgres:14.1-alpine

# Set environment variables for the PostgreSQL database
ENV POSTGRES_DB=db \
    POSTGRES_USER=usr \
    POSTGRES_PASSWORD=pwd

# Copy SQL scripts into the container's initialization directory
COPY 01-CreateScheme.sql docker-entrypoint-initdb.d/01-CreateScheme.sql
COPY 02-InsertData.sql docker-entrypoint-initdb.d/02-InsertData.sql
