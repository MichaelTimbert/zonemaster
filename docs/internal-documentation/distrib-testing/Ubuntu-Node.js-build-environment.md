# Ubuntu Node.js Build Environment

The requirements to build the Zonemaster-GUI distribution zip file are Node.js
and npm. Below are instructions to create such a build environment on Ubuntu.

Node.js and npm are available from the [Node.js] official website. Minimal version
of Node.js is 10.0 but install the latest LTS version available. The process has
been tested on Ubuntu 18.04, which we use here.

1. Make a clean installation of Ubuntu 18.04.

2. Update the package database.
   ```sh
   sudo apt-get update
   ```

3. Install packagages needed
   ```sh
   sudo apt-get install build-essential
   ```

4. Install Node.js by using [NVM], a node version manager.
   ```sh
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
   nvm install 14.17.0
   nvm use 14.17.0
   ```

5. After installation, log out and log in again to handle [known issue], or just:

   ```sh
   source ~/.bashrc
   ```


[known issue]:                          https://github.com/nvm-sh/nvm#troubleshooting-on-linux
[Node.js]:                              https://nodejs.org/en/
[NVM]:                                  https://github.com/nvm-sh/nvm

