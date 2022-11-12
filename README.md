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
      passes:
      - upstream: "http://localhost:8080/"
        auth: true
        allowed_domains:
        - "192.168.0.0/24"

### Proxy headers and attributes

You can specify list of proxy headers using `proxy_headers` attribute (those will be inserted in your host configuration in format `proxy_set_header <name> <value>;`) or other proxy attributes with custom name and value with `other_proxy_attributes` (resulting in `<name> <value>;` in your host's configuration). See this example:

    nginx_hosts:
    - name: mysite.example.com
      passes:
      - upstream: "http://localhost:8080/"
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
      passes:
      - upstream: "http://localhost:8080/"
        headers:
          - name: Access-Control-Allow-Origin
            value: *

## Load balancer

With this role, you can even setup load balancer. For more information see [templates/nginx.conf.j2](templates/nginx.conf.j2) and [templates/hosts/host.https.conf.j2](templates/hosts/host.https.conf.j2).

    - name: kvm.pod.cvut.cz
      load_balance:
        protocol: "https"
        servers:
        - name: "kvm01.pod.cvut.cz:8006"
        - name: "kvm02.pod.cvut.cz:8006"
        - name: "kvm03.pod.cvut.cz:8006"
        - name: "kvm04.pod.cvut.cz:8006"
        - name: "kvm05.pod.cvut.cz:8006"
        - name: "kvm06.pod.cvut.cz:8006"
        - name: "kvm07.pod.cvut.cz:8006"
        - name: "kvm08.pod.cvut.cz:8006"
        - name: "kvm09.pod.cvut.cz:8006"
        - name: "kvm10.pod.cvut.cz:8006"
        method: ip_hash
      proxy_headers:
      - ...
