# Installation
1. Clone this repository
2. Put all files into a directory which is in your PATH (e.g. $HOME/bin

# Usage
1. Set the environment variable REST_ENDPOINT to the FQDN of you AVI controller (virtual IP)
2. (Optional) In order to avoid typing in username and password every time, create a file $HOME/creds/<FQDN of AVI>.auth in the following format:

username:password
1

##Beware of multi user systems, make sure your .auth file has proper permissions and is only readable for you
