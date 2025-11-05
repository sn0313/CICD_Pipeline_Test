# Use PHP CLI as base image
FROM php:7.4-cli

# Copy application code
COPY . /usr/src/myapp
WORKDIR /usr/src/myapp

# Expose port if you plan to run a web server like PHP's built-in server
EXPOSE 8080

# Command to run the PHP built-in web server (you can adjust port/path)
CMD ["php", "-S", "0.0.0.0:8080", "-t", "."]
