---
layout: post
title: "Dockerize Vue.js App"
author: "Abdelrahman Hosny"
---

> Before reading, check the date of the post. This post might become outdated a few weeks later as technology is rapidly changing and official documentations are always improving. 


We have been playing around with Vue.js framework for a while and the team have been very happy with it thus far. Now, we came to the point where we want to enable a CI/CD workflow for our frontend apps. Since we already utilize Docker on our backend services, we wanted to take benefit of the same containerization technology. We looked up at the [Vue.js](https://vuejs.org/v2/cookbook/dockerize-vuejs-app.html) documentation on this topic, and started from there. 

However, it took us some time to prepare a production ready Docker image for our app and wanted to share what we got.

# A Dockerfile that works!
The documentation provided a [real-world example](https://vuejs.org/v2/cookbook/dockerize-vuejs-app.html#Real-World-Example) but it needs some tweaks to make it work. So, here is our modified Dockerfile:

{% highlight shell  linenos %}
# build stage
FROM node:9.11.1-alpine as build-stage
LABEL MAINTAINER Abdelrahman <abdelrahman@xware.co>
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM nginx:1.13.12-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html/dist
COPY --from=build-stage /app/index.html /usr/share/nginx/html
COPY --from=build-stage /app/src/assets /usr/share/nginx/html/src/assets
COPY --from=build-stage /app/nginx/default.conf /etc/nginx/conf.d
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

{% endhighlight %}

As you see, it is largely adopted from the official documentation. Here are the modifications/additions:
- In line 12, we modified the copy location in the Docker image to be `/usr/share/nginx/html/dist` 
- In line 13, we copied the index file `/app/index.html` to `/usr/share/nginx/html`
This lets the app serve the default website at `/usr/share/nginx/html` by looking for the `index.html` file. The `index.html` then looks for `.js` files located in `dist` folder in the same directory.

What about other static assets?

# Serving Static Assets
Whether you have static images, css files, fonts or JS files, you need to serve them within the same app. If you are not using a CDN, you need the Docker image to be self-inclusive of all its content and serve those static files by nginx. So, you need to copy the static content into the Docker image, and this is what we did in line 14 above: `COPY --from=build-stage /app/src/assets /usr/share/nginx/html/src/assets`. Modify the `from` and `to` paths to match the paths you save your static files into in your local project.

# A Single Page App
Now, this works greatly, but we face one problem. Vue.js builds single page apps. All we get at the end is one file `index.html`. If you tried the above Docker image, and navigated directly to `http://localhost:8080/some-other-page`, it won't show up. Why? Because this route cannot be found by nginx. Nginx only sees `index.html`. So, what we need to do is to tell nginx to delegate routing lookup to Vue.js controller to take care of this. Therefore, we need to modify the default nginx configuration as we did in line 15 above. We have overriden the default configuration with the below `default.conf`. Note that the only change we made is in line 12: `try_files $uri $uri/ /index.html;`, which tells nginx to try URIs from within the `index.html` file.

{% highlight shell  linenos %}
# build stage
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

{% endhighlight %}

# Build and Run
Now, you have a production-ready nginx Docker image to serve your Vue.js app. Build it:

{% highlight shell %}
docker build -t vuejs-cookbook/dockerize-vuejs-app .
{% endhighlight %}

And run it:
{% highlight shell %}
docker run -it -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
{% endhighlight %}


# Want help dockerizing your Vue.js app?
Tweet me on [@_ahosny](http://twitter.abdelrahmanhosny.me) or email me at [abdelrahman@xware.co](mailto:abdelrahman@xware.co)

Thanks for reading!
