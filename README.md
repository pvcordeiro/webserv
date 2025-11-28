# WebServ - 42 School Project

A custom HTTP/1.1 web server implementation written in C++98, capable of serving static files, handling CGI scripts, and managing multiple routes and configurations.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Supported HTTP Methods](#supported-http-methods)
- [CGI Support](#cgi-support)
- [Error Handling](#error-handling)
- [Testing](#testing)

## 🎯 Overview

This project implements a non-blocking HTTP/1.1 web server from scratch, following the 42 School curriculum requirements. The server uses **epoll** for efficient I/O multiplexing and supports multiple virtual hosts, CGI execution, file uploads, and custom error pages.

## ✨ Features

- **HTTP/1.1 Protocol** support with persistent connections
- **Non-blocking I/O** using epoll
- **Multiple virtual servers** on different ports
- **CGI execution** for dynamic content (Python, PHP, Ruby)
- **File uploads** with configurable directories
- **Static file serving** with directory listing
- **Custom error pages** (400, 403, 404, 405, 413, 500, 504)
- **Route-based routing** with wildcard support
- **HTTP redirections** (301/302)
- **Request body size limiting**
- **Keep-alive connections**
- **Method restrictions** per route

## 🔧 Requirements

- **C++ Compiler** with C++98 standard support
- **POSIX-compliant system** (Linux/macOS)
- **Python 3** (for CGI scripts)
- **PHP-CGI** (optional, for PHP CGI support)
- **Ruby** (optional, for Ruby CGI support)

## 📦 Installation

1. Clone the repository:

```bash
git clone https://github.com/pvcordeiro/webserv.git
cd webserv
```

2. Compile the project:

```bash
make
```

3. The executable `webserv` will be created in the project root.

## ⚙️ Configuration

The server uses a JSON configuration file to define server behavior. By default, it looks for:

- `./server.json`
- `./webserv.conf`
- `./config.json`

### Configuration Structure

```json
{
  "servers": [
    {
      "port": 8080,
      "host": "localhost",
      "max_body_size": 52428800,
      "extensions": [
        { "ext": "py", "exec": "/usr/bin/python3" },
        { "ext": "php", "exec": "/usr/bin/php-cgi" },
        { "ext": "rb", "exec": "/usr/bin/ruby" }
      ],
      "routes": [
        {
          "path": "/*",
          "directory": "www",
          "methods": ["GET", "POST", "DELETE"],
          "directory_listing": true,
          "index": ["index.html"]
        }
      ],
      "error_pages": [{ "code": 404, "page": "/404.html" }]
    }
  ]
}
```

### Configuration Options

#### Server Level

- `port` - Port number to listen on
- `host` - Host address (e.g., localhost, 0.0.0.0)
- `max_body_size` - Maximum request body size in bytes
- `extensions` - CGI interpreter mappings
- `routes` - Array of route configurations
- `error_pages` - Custom error page mappings

#### Route Level

- `path` - Route path (supports wildcards: `/path/*`)
- `directory` - Root directory for serving files
- `methods` - Allowed HTTP methods (GET, POST, DELETE, PUT, etc.)
- `index` - Default index files
- `directory_listing` - Enable/disable directory listing
- `cgi` - Enable CGI execution for this route
- `upload_dir` - Directory for file uploads
- `redirect` - URL for HTTP redirection

## 🚀 Usage

### Starting the Server

```bash
# Use default configuration
./webserv

# Specify configuration file
./webserv path/to/config.json
```

### Example Requests

#### Test Page (Frontend)

```bash
# Test page is available on port 1146 with all HTTP methods enabled
# Open in your browser:
# http://localhost:1146/test.html
# or simply:
# http://localhost:1146/
```

#### Serve Static Files

```bash
curl http://localhost:8080/index.html
```

#### Upload a File

```bash
curl -X POST -F "file=@myfile.txt" http://localhost:8080/upload
```

#### Execute CGI Script

```bash
curl http://localhost:8080/cgi/script.py
```

#### Delete a File

```bash
curl -X DELETE http://localhost:8080/uploads/myfile.txt
```

## 🔄 Supported HTTP Methods

- **GET** - Retrieve resources
- **POST** - Upload files or submit data
- **DELETE** - Remove files
- **PUT** - Update resources (via CGI)
- **PATCH** - Partial updates (via CGI)
- **OPTIONS** - Check allowed methods (via CGI)

## 🔌 CGI Support

The server supports CGI 1.1 specification with the following features:

### Supported Languages

- **Python** (.py)
- **PHP** (.php)
- **Ruby** (.rb)

### Environment Variables

Standard CGI environment variables are passed to scripts:

- `REQUEST_METHOD`
- `QUERY_STRING`
- `CONTENT_TYPE`
- `CONTENT_LENGTH`
- `SCRIPT_FILENAME`
- `PATH_INFO`
- `SERVER_PROTOCOL`
- `HTTP_*` headers

### Example CGI Script (Python)

```python
#!/usr/bin/env python3
print("Content-Type: text/html")
print()
print("<html><body>")
print("<h1>Hello from CGI!</h1>")
print("</body></html>")
```

## ⚠️ Error Handling

### Supported Error Codes

- **400** - Bad Request
- **403** - Forbidden
- **404** - Not Found
- **405** - Method Not Allowed
- **413** - Request Entity Too Large
- **500** - Internal Server Error
- **504** - Gateway Timeout

Custom error pages can be configured per server in the configuration file.

## 🧪 Testing

### Manual Testing

Use the provided `request.http` file with REST client extensions (VS Code REST Client, etc.).

### Command Line Testing

```bash
# Test static file serving
curl -v http://localhost:8080/

# Test upload
curl -v -X POST -F "file=@test.txt" http://localhost:8080/upload

# Test CGI
curl -v http://localhost:8080/cgi/script.py

# Test directory listing
curl -v http://localhost:8080/uploads/

# Test non-existent file (404)
curl -v http://localhost:8080/nonexistent.html

# Test method not allowed
curl -v -X PUT http://localhost:8080/index.html
```

### Stress Testing

```bash
# Apache Bench
ab -n 1000 -c 10 http://localhost:8080/

# Siege
siege -c 10 -r 100 http://localhost:8080/
```

## 📝 Notes

- The server is compiled with `-std=c++98` to comply with 42 School requirements
- Non-blocking I/O ensures the server never hangs
- CGI timeout is set to prevent indefinite script execution
- Request body size limits prevent memory exhaustion attacks
- The implementation follows HTTP/1.1 RFC 2616 specifications

## 📄 License

This project is part of the 42 School curriculum.

## 👨‍💻 Authors

**pvcordeiro** - [GitHub](https://github.com/pvcordeiro)

**afonsopc** - [GitHub](https://github.com/afonsopc)

---

_Made with ☕ at 42 School_
