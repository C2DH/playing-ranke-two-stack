
# Prerequisites: Docker & Git

## Docker
In order to be able to use the folowing instructions, you will need to have *docker* set up and running on your computer. Manuals on how to install *docker* on various opararting systems can be found here: https://docs.docker.com/desktop/

If you install the tools seperately, you will need both *docker engine* and *docker compose*.
If you install *docker desktop*, those two will be included already. It will also allow you to use docker without the command line, but the following examples presuppose that you are using the command line.

We have tested the set-up on Windows, Mac, and Linux (Ubuntu).

## Git
You will also need to have *Git* installed on your computer. Instructions can be found here: https://github.com/git-guides/install-git

# playing-ranke-two-stack

Git clone this repository and create a local copy on your computer.

Click on the green button at the top of this webpage and copy the URL of the repository to your clipboard. Open a terminal/command propmt and navigate to the directory into which you want to insert the repository. Type "git clone" and paste the URL (on Linux you need to use Ctrl+Shift+C/V for copy-pasting):

```
git clone https://github.com/C2DH/playing-ranke-two-stack.git
```
Refer to GitHub Docs if you are not familiar with git commands: https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository

## Installation warm up

Create a `config/application-production.properties` file in the `config` folder following the example properties file.
adjust the SMTP config according to your system:

```
marugoto.fromMail=<your-from-mail>

smtp.host=<your-smtp-host>
smtp.port=<your-smtp-port>
smtp.username=<your-smtp-username>
smtp.password=****
```
(You don't need to delete the example properties file afterwards, it will be git-igonred.)

## Install an existing game

You can clone any of the existing marugoto games found here: https://github.com/uzh/lit-content

For example, **clone the lit-demo** project to your local folder `data/marugoto/contents` (this folder is git-ignored, except for ranketwo game-content):

```
cd data/marugoto/contents #navigate to the correct folder
git clone git@github.com:uzh/lit-demo.git #git clone the repo
```

## Launch the stack

To start the docker containers and launch the app, first navigate to the directory which contains the docker-compose.yml file in your terminal/command prompt.

Then type:


```
docker-compose up
```

(You can use the -d flag to run the command in "detached mode" i.e. run containers in the background: ```docker-compose up -d```)

Check the **network name** of the docker stack using:

```
docker network ls
```

Note down the playing ranke 2 network name, in our case `playing-ranke-two-stack_default`

Run the `uzhlit/marugoto-shell` to access the database and perform initial operations

```
docker run -i \
  --network=playing-ranke-two-stack_default \
  -v "$PWD"/data/marugoto/contents:/home \
  -v "$PWD"/config/application-production.properties:/etc/application-production.properties \
  uzhlit/marugoto-shell
```

You should get a nice marugoto shell prompt:

```
marugoto:>
```

First thing firsts, create a valid user:

```
marugoto:>create-user name lastname email password
```

Then import the lit-demo to the arangodb (please make sure the docker stuck is up and running :)

Two operations: create a user with `create-user name lastname email password` and

```
marugoto:>do-import /home/lit-demo-2 lit-demo false
```

Check the [marugoto wiki](https://github.com/uzh/marugoto/wiki) for more info bout the game

If you use doker-compose locally, point your browser to http://localhost:8088 to start playing.

## Create your own game on this stack

Create a new folder in your `./data/marugoto/contents` folder following the marugoto guidelines.
THi involves at least the creation of a `topic.json` and a `startPage` json files as starting point, see for instance how `ranketwo`:

```
./data/marugoto/contents/ranketwo

{
  "id": null,
  "title": "Playing Ranke",
  "image": "resources/image/RankeScreen.jpg",
  "active": true,
  "startPage": "chapter1/page1/page.json"
}
```

All local resources (basically images) **must** go to a `./data/marugoto/contents/<your game>/resources/image`
folder and then mapped to a _docker volume_ in the mail docker-compose file.

In this case, right after this line:

```
volumes:
  - ./data/marugoto/contents/ranketwo/resources:/usr/share/nginx/html/ranketwo
```

add the same folder name to both local and mapped volume, like this:

```diff
volumes:
   - ./data/marugoto/contents/ranketwo/resources:/usr/share/nginx/html/ranketwo
+  - ./data/marugoto/contents/myawesomegame/resources:/usr/share/nginx/html/myawesomegame
```
