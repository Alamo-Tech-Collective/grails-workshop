```
your-host.com {
    tls brandon@zelifcam.net
    reverse_proxy localhost:8080 {
        header_up -X-Forwarded-Proto
        header_up Host {host}
        header_up X-Forwarded-Scheme {scheme}
        header_up X-Forwarded-For {remote}
        header_up X-Real-IP {remote}
    }
}
```
