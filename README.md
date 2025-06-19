# MySQL Database of R3MOB

The objective is to conteneurize the *MySQL* database, and also to manage it
using an *adminer*.

## Prerequisites

1. [***docker***](https://docs.docker.com/engine/install/)

2. [***docker compose***](https://docs.docker.com/compose/install/linux/)

## Start the database and the adminer

### You want first to edit the environment variables

You can edit these variables using:

```bash
cd server_database/
cp .env.example .env
vim .env # edit the variables.
```

### Then you want to start the database and the adminer

```bash
cd server_database/ 
docker compose -f docker-compose.yml up -d
```

### Now you want to create the database

You can do this:

```bash
docker exec -it mysql-r3mob mysql -uroot -pCHANGEME -e "CREATE DATABASE r3mob_dev CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;"
```

or you can go on the *adminer* webpage and enter the following *SQL* command:

```mysql
CREATE DATABASE r3mob_dev CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### Then you want to import the database

You can do this:

```bash
docker exec -i mysql-r3mob mysql -uroot -pCHANGEME r3mob_dev < /path/to/database_r3mob.sql
```

## MySQL credential

- Username: root

- Password: CHANGEME

## How to connect to MySQL

### Via Adminer

Go to ``http://localhost:3320``. (pour le port, il faut utiliser ce qu'il y a dans le .env (ADMINER_PORT))

### Via command line

```bash
docker exec -it mysql-r3mob bash
mysql -uroot -pCHANGEME
```

## Stop the database and the adminer

If you want to shutdown one container, you can do this:

```bash
cd server_database/
docker stop adminer-r3mob # Stop the adminer.
docker stop mysql-r3mob # Stop the database.
```

If you also want to remove the containers, you can do that:

```bash
cd server_database/ 
docker compose -f docker-compose.yml down
```

Usually, the database is still in ``server_database/var/lib``.

## Miscellaneous

How to create a user in the database?

1. His credentials will be:

- username: "jean.dupont@univ-pau.fr"

- password: "motdepasse"

2. Do this:

```mysql
SET @personnelId = UUID();

INSERT INTO Personnels (
    id,
    username,
    email,
    password,
    iconPath,
    role,
    isPilote,
    isActive,
    isComitePilotage,
    isComiteExecutif,
    createdAt,
    updatedAt,
    isEquipe
) VALUES (
    @personnelId,
    'Jean Dupont',
    'jean.dupont@univ-pau.fr',
    NULL,
    NULL,
    NULL,
    0,
    1,
    0,
    0,
    NOW(),
    NOW(),
    0
);
```

```mysql
INSERT INTO Users (
    password,
    temporaryPassword,
    resetPasswordToken,
    imageName,
    createdAt,
    updatedAt,
    PersonnelId
) VALUES (
    '$2b$10$YI3Uu60pbRx8inZzdMNHhOqTASkETKZViodxmb45M8DqeDBNeEFNm',
    0,
    NULL,
    NULL,
    NOW(),
    NOW(),
    @personnelId
);
```

3. Restart the server

### EOF

