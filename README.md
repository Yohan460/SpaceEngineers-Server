# Space Engineers on Linux
Docker container for running a Space Engineers server on a Linux machine.

## Requirements
- Debian 8 (to use these files as-is)
- docker (this one is obvious)
- systemd (you can use System V init, but you will have to write your own startup script).

## Setup
Perform the following steps in the written order.

- Create a new system user account which will own the container. The files in this repository assumes that the new user is named `saiban`. You can of course change this to whatever you prefer. Please note that the user ID must correspond to the ID specified in the Dockerfile. If you change it here, you should change it there.

```bash
adduser --system --home /home/se-server --uid 256 --group --gecos "Space Engineers" se-server
```

- Change to the new user and move into its home directory by issuing the following commands.

```bash
su - se-server -s /bin/bash
cd
 ```
- Create the required directory structure.

```bash
mkdir -p games/space-engineers/{docker,data}
mkdir -p games/space-engineers/data/Space\ Engineers/{Mods,Saves}
```

- Clone this repository into the home directory of the new user account.

```bash
git clone https://github.com/g1franc/se-server.git games/space-engineers/docker
```

- Obtain a copy of the most current `DedicatedServer.zip` and place it in `~/games/space-engineers/data`. You will usually find it in `[...]\SteamApps\SpaceEngineers\Tools\DedicatedServer.zip` (do not extract its contents).

- Upload your `SpaceEngineers-Dedicated.cfg` and place it in `~/games/space-engineers/data/Space Engineers`. Use the one in this repository and edit it to your liking if you do not already have one or copy the one from the repository
:
```bash
cp ./games/space-engineers/docker/SpaceEngineers-Dedicated.cfg ./games/space-engineers/data/Space\ Engineers
```

- Copy `space-engineers.service` from this repository to `/etc/systemd/system`. Do not forget to change the `User` and `Group` setting in this file if you did not go with the defaults.

```bash
sudo cp games/space-engineers/docker/space-engineers.service /etc/systemd/system
```

- **Build the image!**

This will take a while.

```bash
games/space-engineers/docker/build.sh
```

## Updating
Update Space Engineers by replacing the `DedicatedServer.zip` with an updated version and restarting the server.

## Managing the server

### Starting the server

```bash
sudo systemctl start space-engineers
```

### Stopping the server

```bash
sudo systemctl stop space-engineers
```

### Monitoring the server
This will give you live output from the server, as if it was running locally in your terminal.

```bash
sudo journalctl --no-tail -f -u space-engineeers
```

## Additional notes
Running Space Engineers this way requires a helper program called `sigmap` which I developed for this use specifically. It is cloned and built by the Dockerfile and should not add any additional overhead. Because Space Engineers ignores `SIGTERM` which is sent by Docker to shut down the service, `sigmap` catches that signal and forwards `SIGINT` to Space Engineers.

See [here](https://github.com/marjacob/sigmap "sigmap") for more information.

## Credits
The original `Dockerfile` was written by [webanck](https://github.com/webanck "webanck") and can be found [here](https://github.com/webanck/docker-wine-steam "Steam with Docker").
