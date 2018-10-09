# Gitea1.0
This is a our first attempt at Gitea integration
Setting up Gitea

Done on MacOSX Linux Terminal (*Indentation is critical*)

Install your ssh key into Thoughtwire database

	1. Set up ‘ssh cr1.ds.twamb.ca -l twops’ on your bash acc
		1.a. Enter your pass for key '/Users/USERNAME/.ssh/id_rsa':

Installing Docker Compose

	1. Run this command  to download latest version 
	sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

    	2.  Apply executable permissions to the binary
	sudo chmod +x /usr/local/bin/docker-compose

	3. Test installation with 
	docker-compose --version

			3.a. Expected result 
        	—> docker-compose version 1.22.0, build 1719ceb

	Prerequisites

	1. Create a directory for the project:
		mkdir composetest
		cd composetest

    2. Create a file called app.py in your project directory and paste this in:
	import time

	import redis
	from flask import Flask


	app = Flask(__name__)
	cache = redis.Redis(host='redis', port=6379)


	def get_hit_count():
   		 retries = 5
   		 while True:
       			 try:
           			 return cache.incr('hits')
        			except redis.exceptions.ConnectionError as exc:
           			 if retries == 0:
                				raise exc
           			 retries -= 1
           			 time.sleep(0.5)


	@app.route('/')
	def hello():
    		count = get_hit_count()
   		 return 'Hello World! I have been seen {} times.\n'.format(count)

	if __name__ == "__main__":
    		app.run(host="0.0.0.0", debug=True)

    3. Create another file called requirements.txt in your project directory and paste this in:
	flask
	redis

4. In your project directory, create a file named Dockerfile and paste the following:
	FROM python:3.4-alpine
	ADD . /code
	WORKDIR /code
	RUN pip install -r requirements.txt
	CMD ["python", "app.py"]
5. Create a file called docker-compose.yml in your project directory and paste the following:
	version: "2"

	networks:
 	  gitea:
    	    external: false

	services:
 	  server:
    	    image: gitea/gitea:latest
    	    environment:
      	      - USER_UID=1000
      	      - USER_GID=1000
    	    restart: always
    	    networks:
      	        - gitea
    	    volumes:
      	        - ./gitea:/data
    	    ports:
      	        - "3000:3000"
      	        - "222:22"
	Start
	1. Execute ‘docker-compose up -d’ to launch Gitea in the background
	2. Use ‘docker-compose ps’ to show if Gitea started properly
	3. Logs can be viewed through ‘docker-compose logs’
	4. Run the server by http://localhost:3000/
		localhost will be the authenticity of host ‘cr1.ds.twamb.ca(localhost)’

Features
* User Dashboard
    * Context switcher (organization or current user)
    * Activity timeline
        * Commits
        * Issues
        * Pull requests
        * Repository creation
* Issues dashboard
    * Context switcher (organization or current user)
    * Filter by
* Pull request dashboard
    * Same as issue dashboard
* Repository types
* Explore page
    * Users
    * Repos
    * Organizations
    * Search
* Custom templates
* Override public files (logo, css, etc)
* Logging
* Configuration
    * Databases
    * Configuration file
        * app.appy
    * Environment variables
    * Command line options
* Users
    * Profile
        * Name
        * Username
        * Email
        * Website
        * Join date
        * Followers and following
        * Organizations
        * Repositories
        * Activity
        * Starred repositories
    * Settings
* Repositories
    * Clone with SSH/HTTP/HTTPS
    * Code
        * Branch browser
        * Download
        * Web based editor
        * View file history
        * Delete file
        * View raw
    * Issues
    * Pull requests
    * Commits
    * Settings
	
