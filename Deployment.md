# Deployment and Stopping Instructions

## Running the Script

Go to your project directory which have a Dockerfile. 
Run command `./deploy --docker`

<!-- ## Stopping the Sript

Go to your projecct directory which have a Dockerfile. 
Run command `stop` -->

Go to your project directory which have a python file. 
Run command `./deploy file.py`


for calling deploy from any directory
`sudo nano /usr/local/bin/deploy`  and copy the content of deploy.sh
`sudo chmod +x /usr/local/bin/deploy` for permissions to execute

Do same for stop.sh

now simply run `deploy --docker` or `deploy file.py`
