
# Prerequisites: Docker & Git

## Docker
In order to be able to use the folowing instructions, you will need to have *docker* set up and running on your computer. Manuals on how to install *docker* on various opararting systems can be found here: https://docs.docker.com/get-docker/

If you install the docker tools seperately, you will need both *docker engine* and *docker compose*.
If you install *docker desktop*, those two will be included already. It will also allow you to use docker without the command line, but the following examples presuppose that you are using it.

We have tested the set-up on Windows, Mac, and Linux (Ubuntu).

**Alternative set-up**: The alternative to *docker* is that you install all the individual pieces of the software on your computer being extra careful to install **exactly** the required version of each item (version 10 of JDK, ArangoDB 3.2.15, version 10 of Node.js). There is a detailed description here: https://github.com/uzh/marugoto/wiki/Development-Environment

You will also need to use a Java IDE (like Spring Tools) to manage data import.
This set-up has been confirmed to work on Mac and Linux.

## Git
You will also need to have *Git* installed on your computer. Instructions can be found here: https://github.com/git-guides/install-git

# Playing-ranke-two-stack

Git clone this repository and create a local copy on your computer.

Click on the green button at the top of this webpage and copy the URL of the repository to your clipboard. Open a terminal/command prompt and navigate to the directory into which you want to insert the repository. Type "git clone" and paste the URL (on Linux you need to use Ctrl+Shift+C/V for copy-pasting):

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

## Download an existing game

You can clone any of the existing marugoto games found here: https://github.com/uzh/lit-content

For example, **clone the lit-demo** project to your local folder `data/marugoto/contents` (this folder is git-ignored, except for ranketwo game-content for content editors):

```
cd data/marugoto/contents #navigate to the correct folder ("Set-Location" on PowerShell instead of "cd")
git clone git@github.com:uzh/lit-demo.git #git clone the repo
```

## Launch the stack

To start the docker containers and launch the app, first navigate to the directory which contains the docker-compose.yml file in your terminal/command prompt.

Then type:


```
docker-compose up
```

(You can use the -d flag to run the command in "detached mode" i.e. run containers in the background: ```docker-compose up -d```)

After you docker-compose up, 4 containers will be running in docker (web, database, backend, and shell). The shell container will stop soon afterwards, but don't worry about that.

## Run the shell and import game data

Now you need to run the shell individually. Navigate to the directory that contains the docker-compose.yml again, or remain in the same terminal, if you have been using "detached mode". Type:

```
docker-compose run shell
```
You should get a nice marugoto shell prompt:

```
marugoto:>
```
(You can type "help" to get some info on available commands and their syntax within the shell.)

### Create a user
```
marugoto:>create-user name lastname email password
```

For example:
```
marugoto:>create-user Leopold Ranke Ranke@Berlin ArchivalTurn22
```

It doesn't need to be a real email address nor your real name. This will be your in-game name and various game characters and institutions will use it to address you.

### Import game data

Now import the game data to the database by:
1. indicating the path to the game on your computer
2. giving the imported game a personal alias (**use "my-ranke" for ranketwo and "my-demo" for lit-demo**)
3. boolean for deleting player state or not

For example:
```
marugoto:>do-import /home/lit-demo my-demo false
```
**Note:** *You need to use the exact game aliases "my-ranke" and "my-demo" in order for the images to be displayed in the app. If you want to import another game than ranketwo and lit-demo, you will need to modify the docker-compose.yml file by adding the following line:*

```
- ./data/marugoto/resources/"game-alias":/usr/share/nginx/html/"game-alias"
```

right under this line:

```
web:
    image: c2dhunilu/marugoto-frontend:latest
    volumes:
      - ./data/marugoto/resources/my-demo:/usr/share/nginx/html/my-demo
      - ./data/marugoto/resources/my-ranke:/usr/share/nginx/html/my-ranke
```

A successful & completed data import should look like this:

<img width="580" alt="Screenshot 2022-08-29 at 18 16 59" src="https://user-images.githubusercontent.com/53467834/187247152-a91fa911-4a7d-4d97-9ce1-28832dca2a81.png">

This will create a new hidden folder with the alias you specified. To see hidden folders use command-shift-dot on Mac or the folder-view-menu on Windows.

To verify if any of the games have been properly imported, try:


```
marugoto:>list-topics

```
The output should look like this:

<img width="582" alt="Screenshot 2022-08-29 at 18 23 55" src="https://user-images.githubusercontent.com/53467834/187248383-c6b12b7d-7c3e-4cfb-b440-b02810f166f5.png">

If the list is empty, then the import didn't work. In that case you won't see any games in the topic selection screen in the next step.

### Open web app and start playing
In your browser go to: http://localhost:8088 to start playing. Use the credentials you created with the shell earlier to log in.

If you experience some issues with the images and media files, check out our issues section to find some possible workarounds.

# Create your own game on this stack

You can create your own game on this stack by following a few structural guidelines here: https://github.com/uzh/marugoto/wiki/Content

You can also play the LiT tutorial game on https://livesintransit.org to playfully learn about game structure and components.

At the very least, you will need to create a topic.json file, like in this example:

```
{
  "id": null,
  "title": "Playing Ranke",
  "image": "resources/RankeScreen.jpg",
  "active": true,
  "startPage": "chapter1/page1/page.json"
}
```
Put images, audio, and video files in a separate "resources" folder. You will have to indicate the path to your resources in the JSON files (like the path to RankeScreen.jpg in the topic.json example above).
Create a chapter folder and a few pages within it (usually containing some text components, image components, exercise components, page transitions ect.) and indicate the path to the "startPage" in the topic.json file. 
