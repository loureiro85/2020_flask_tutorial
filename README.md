# 2020_flask_tutorial


## Technologies
- Python
- Flask
- Gunicorn
- Nginx

## Cloud platform
- Virtual Machine at Linode


## Tasks Deploy to a Linux Server



# Notes

## Roles of Flask, Gunicorn and NGINX
Comment from Lightning in My Hands [https://www.youtube.com/watch?v=goToXTC96Co&list=PL-osiE80TeTs4UjLw5MM6OjgkjFeUxCYH&index=13]
1 year ago (edited)
I think it's important to make the following distinctions after we switch from using Flask's local dev server to web-facing NGINX/Gunicorn structure:

Our Flask app is a **WSGI app** . It generates dynamic content (ex: user posts, user names). The way it can do this is by accessing the database (i.e. using SQLAlchemy).
Gunicorn is a **WSGI server** which launches the WSGI app. In this video, Gunicorn is also a manager of 3 workers which each launches one instance of the WSGI app.
NGINX  is a **web server** which generates static content (ex: navigation bar, page name). This also delivers HTTP requests to Gunicorn.