# NGINX Playground Configuration

This repository contains an NGINX configuration file (`nginx.conf`) that addresses three specific problem statements given in the assessment for Nginx playground. The configurations aim to fulfill the following requirements:

1. Add location-based config to return a 404 for specific file extensions.
2. Log specific fields in the access.log using a custom log format.
3. Set HTTP security headers in NGINX only if the header value is not set in the response from the upstream. The default is set to none, and headers are only set if nothing is returned from the upstream.

## Table of Contents

1. [Problem Statements](#problem-statements)
2. [Solution Details](#solution-details)
   - [1. Location-Based Config for 404](#1-location-based-config-for-404)
   - [2. Logging Fields in Access.log with log_format](#2-logging-fields-in-accesslog-with-log_format)
   - [3. HTTP Security Headers Based on Upstream Response](#3-http-security-headers-based-on-upstream-response)
3. [Testing](#testing)
4. [Additional Notes](#additional-notes)

## Problem Statements

### 1. Location-Based Config for 404

**Requirement:** Add location-based configuration to return a 404 for any content that ends with "css, jpg, jpeg, js, json, png, mp4, pdf" when accessed.

**Solution:** The NGINX configuration includes a server block with a location directive that uses a regular expression to match file extensions. If a request matches any of the specified extensions, a 404 response is returned.

### 2. Logging Fields in Access.log with log_format

**Requirement:** Configure NGINX to log specific fields in the access.log file using a custom log format.

**Solution:** The NGINX configuration includes a `log_format` directive named `custom_format` that logs various fields in the access.log file. This log format includes details such as the time, NGINX version, remote address, request ID, status, user agent, and more.

### 3. HTTP Security Headers Based on Upstream Response

**Requirement:** Add HTTP security headers at NGINX only if the header value is not set in the response from the upstream. The default is set to none, and headers should be set only if nothing is returned from the upstream.

**Solution:** The NGINX configuration includes a location block where HTTP security headers are conditionally set using the `add_header` directive. Headers are set only if the corresponding variable (`$sent_http_`) from the upstream response is empty.

## Solution Details

### 1. Location-Based Config for 404

**Configuration:**
```nginx
location ~* \.(css|jpg|jpeg|js|json|png|mp4|pdf)$ {
    return 404;
}


2. Logging Fields in Access.log with log_format
Configuration:

log_format custom_format '$time_local - Nginx Version: $nginx_version - '
                       '$remote_addr - $request_id - $status - $body_bytes_sent - '
                       '"$http_user_agent" - $proxy_protocol_addr - $server_name - '
                       '$upstream_addr - $request_time - $upstream_connect_time - '
                       '$upstream_header_time - $upstream_response_time - $request_uri - '
                       '$upstream_status - $ssl_session_reused - "$http_x_forwarded_for"';



3. HTTP Security Headers Based on Upstream Response
Configuration:

location / {
    proxy_pass http://172.10.1.10; # backend_server

    # Set default values if not present in upstream response
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always if_empty;
    add_header X-Content-Type-Options "nosniff" always if_empty;
    add_header X-XSS-Protection "1; mode=block" always if_empty;
    add_header X-Frame-Options "DENY" always if_empty;
    add_header Content-Security-Policy "frame-ancestors 'none'" always if_empty;
    add_header Access-Control-Allow-Credentials "TRUE" always if_empty;
}

Testing
Testing can be performed using tools like curl to simulate requests and observe the responses based on the defined configurations. For example:

curl http://172.10.1.10/config/file.css


