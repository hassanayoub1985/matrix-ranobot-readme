# Increase Limit File Upload Size in NGINX

## _The Last Markdown Editor, Ever_

By default, Nginx has a limit of 1MB on file uploads. To set file upload size, you can use the client_max_body_size directive, which is part of Nginx’s ngx_http_core_module module. This directive can be set in the http, server or location context.

It sets the maximum allowed size of the client request body, specified in the “Content-Length” request header field. Here’s an example of increasing the limit to 100GB in /etc/nginx/nginx.conf file.

Set in http block which affects all server blocks (virtual hosts).

```
http {
    ...
    client_max_body_size 102400M;
	client_body_timeout 300s;

	client_body_in_file_only clean;
	client_body_buffer_size 16K;
	client_body_temp_path /home/nginx/client_body_temp;
}
```

The last line is the important part - there I tell nginx to fiddle with its files in the /home space.
And restart Nginx server.
