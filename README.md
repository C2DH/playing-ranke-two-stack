# playing-ranke-two-stack

Docker compose to start playing with marugoto and Ranke-2

## Installation warm up

Cerate a `config/application-production.properties` file in the `config` folder folliwing the example properties file.
adjust the SMTP config according to your system:

```
marugoto.fromMail=<your-from-mail>

smtp.host=<your-smtp-host>
smtp.port=<your-smtp-port>
smtp.username=<your-smtp-username>
smtp.password=****
```

Now, **clone the lit-demo** project to your local folder `data/marugoto/contents` (this folder is gitignored):

```
cd data/marugoto/contents
git clone git@github.com:uzh/lit-demo.git
```

Launch the stack using:

```
docker-compose up
```

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

Check the [marugoto wiki](https://github.com/uzh/marugoto/wiki) for more info
