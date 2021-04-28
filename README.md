# Ansible Role: Nginx

An Ansible Role that configures Nginx as reverse proxy on Ubuntu/Debian.

## Requirements

None.

## Role Variables

### HTTP Basic Auth

You can enable HTTP Basic Auth for all domains or whitelist some domains that can access your site without username/password prompt.

Set `nginx_use_htpasswd` to `true` and do not forget to enable auth on your specific nginx host as well as a list of allowed domains. Example:

    nginx_hosts:
        - name: mysite.example.com
            pass: "http://localhost:8080/"
            auth: true
            allowed_domains:
            - "192.168.0.0/24"


### Proxy headers and attributes

You can specify list of proxy headers using `proxy_headers` attribute (those will be inserted in your host configuration in format `proxy_set_header <name> <value>;`) or other proxy attributes with custom name and value with `other_proxy_attributes` (resulting in `<name> <value>;` in your host's configuration). See this example:

    nginx_hosts:
        - name: mysite.example.com
            pass: "http://localhost:8080/"
            proxy_headers:
              - name: Host
                value: $host
              - name: X-Forwarded-For
                value: $proxy_add_x_forwarded_for
              - name: Upgrade
                value: $http_upgrade
              - name: Connection
                value: $connection_upgrade
            other_proxy_attributes:
              - name: proxy_buffering
                value: "off"
              - name: proxy_http_version
                value: "1.1"

### HTTP Headers

You can also specify list of HTTP Headers as list to host configuration, like:

    nginx_hosts:
        - name: mysite.example.com
            pass: "http://localhost:8080/"
            headers:
              - name: Access-Control-Allow-Origin
                value: *

