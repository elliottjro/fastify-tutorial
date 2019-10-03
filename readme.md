# From scratch
### 1. Create github repo
```sh
git init
```
### 2. Create npm project
```sh
npm init
```
### 3. Add fastify
> From https://github.com/fastify/fastify/blob/master/docs/Getting-Started.md
```sh
npm i fastify
```

```js
// Require the framework and instantiate it
const fastify = require('fastify')

const server = fastify({
  logger: true
})

// Declare a route
server.get('/', (request, reply) => {
  reply.send({ hello: 'world' })
})

// Run the server!
server.listen(3000, (err, address) => {
  if (err) {
    server.log.error(err)
    process.exit(1)
  }
  server.log.info(`server listening on ${address}`)
})
```

### 4. Add a logger
> Pino Logger https://github.com/pinojs/pino
```sh
npm i pino
```

### 5. Pretty print logger
```sh
npm i pino-pretty
```

```js
const pino = require('pino')

module.exports = pino({
  level: 'trace',
  prettyPrint: true
})
```

### 6. Add eslint
```sh
npm i eslint
```

We will use airbnb base as an starting point
> https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb-base#usage

```sh
npx install-peerdeps --dev eslint-config-airbnb-base
```

### 7. Add a local docker database
Make sure you have docker
```sh
docker-compose --version
```

Create a docker-compose yml file
```sh
touch docker-compose-dev.yml
```

Ensure spacing is right
```yml
version: '3'
services:
  mariadb:
    container_name: docker-db
    # using a premade image
    image: mariadb:10.1
    # this is the external port (outside of docker)
    expose:
      - 3306
    # this is the mapping between external and internal (inside docker)
    ports:
      - 3306:3306
    volumes:
      # ensure to add data to gitignore so we dont add db to git
      - ./data:/var/lib/mysql
    # add any more environemnt variables (we can also add password / username for mysql here)
    #  Usually this can be passed via pipeline / container orchestration like kubernetes
    environment:
      - MYSQL_ALLOW_EMPTY_PASSWORD=true
```

Add npm scripts so it boots up each time we run dev
```json
{
  "dev": "npm-run-all dev-db:up dev-server:up",
  "dev-db:up": "docker-compose -f docker-compose-dev.yml up -d",
  "dev-db:down": "docker-compose -f docker-compose-dev.yml down",
  "dev-server:up": "npx nodemon index.js"
}
```
