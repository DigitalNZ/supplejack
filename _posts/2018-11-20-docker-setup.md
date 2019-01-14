---
layout: page
title: "Development Setup via Docker"
category: start_docker
date: 2018-11-20 10:47:52
order: 2
---
You can setup Supplejack for use in a development environment using Supplejack Docker for
a quicker setup locally.

Docker implementation of Supplejack stack includes (API, Manager, Worker, MongoDB, Redis and Solr). Repository found here https://github.com/DigitalNZ/supplejack_docker .

### Getting Started
1. [Install Docker, Docker Compose and dependencies](https://docs.docker.com/install/)

3. Clone this project recursively.

    ```bash
    → git clone --recursive https://github.com/DigitalNZ/supplejack_docker.git
    ```

### Running Docker Containers 🏁

1. Go to project directory.

    ```bash
    → cd supplejack_docker
    ```

2. Build Docker containers (This will take a while).

    ```bash
    → docker-compose build
    ```

3.  Run Docker containers (This will take a while as well).

    ```bash
    → docker-compose up
    ```

    If everything goes well, you should see all the containers running.

    ```bash
    → docker ps
    ```

    ```bash
    CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                    NAMES
    b82c59b64d2e        docker_sidekiq   "bundle exec sidekiq  "    57 seconds ago      Up 56 seconds                                sidekiq
    3ed5cf88d125        docker_worker    "bundle exec rails s  "   57 seconds ago      Up 56 seconds       0.0.0.0:3002->3000/tcp   worker
    7e92bb3df879        docker_manager   "bundle exec rails s  "   57 seconds ago      Up 56 seconds       0.0.0.0:3001->3000/tcp   manager
    ae32635f4802        docker_api       "bundle exec rails s  "   58 seconds ago      Up 57 seconds       0.0.0.0:3000->3000/tcp   api
    dad90c3e0039        redis:2.8         "docker-entrypoint.sh"   58 seconds ago      Up 57 seconds       0.0.0.0:6379->6379/tcp   redis
    a4e536734a7b        docker_solr      "bundle exec rake su.."    59 seconds ago      Up 57 seconds       0.0.0.0:8983->8983/tcp   solr
    47fba6738e23        mongo:3.6.8      "/entrypoint.sh mongo"   17 hours ago        Up 57 seconds       27017/tcp                supplejackdocker_mongodb_1
    ```

4. If you exit the docker process for any reason, make sure you run `docker-compose down` before running `docker-compose up` again.  This will ensure the PID'S are correctly reset.

### Seeding Data

The Supplejack components are connected by API keys. There are two users that are now seeded by default when you start things up with docker-compose.

1. a user for the harvester App (Supplejack Manager) in the **sample Api container** with email info@email.com
2. a normal api user in the sample Api container with credentials developer@email.com / password

```
API:
    api_key: 'KJ64DC023FFO' - used for harvests
    api_key: '82HYSEI92N0DGN28' - used for normal api requests
```

There is also now a prepopulated user in the Supplejack Manager container with the following credentials sjdocker@email.com / password which you can use to login to the manager.

Use the appropriate localhost ports to access the services:

```yaml
→ api: http://localhost:3000/records.json?api_key=82HYSEI92N0DGN28
→ manager: http://localhost:3001
→ worker: http://localhost:3002
→ sidekiq: http://localhost:3002/sidekiq
→ solr: http://localhost:8982/solr
```

### Example harvest

Log into your manager at [localhost:3001](http://localhost:3001) and hover over ‘Contributors and Scripts’ in the top navigation, click ‘Parser Scripts’, and then select the parser script named ‘oaisample’ which is a pre-populated parser script. You will now have a screen which will allow you to write your parser script for the harvester, this parser is written in Ruby and has it’s own DSL.[The documentation for the parser DSL can be found here](http://digitalnz.github.io/supplejack/)

You should now be able to preview your parser! Click the preview button and the various components of Supplejack will whurr into a working state.

If your preview is successful, you will see a modal window appear a json object of the potential records. Something like this:

```json
{
  "priority": 0,
  "match_concepts": null,
  "content_partner": [
    "University of Otago"
  ],
  "display_content_partner": [
    "University of Otago"
  ],
  "display_collection": [
    "Otago University Research Heritage"
  ],
  "primary_collection": [
    "Otago University Research Heritage"
  ],
  "collection": [
    "Otago University Research Heritage"
  ],
  "copyright": [
    "All rights reserved"
  ],
  "usage": [
    "All rights reserved"
  ],
  "dc_rights": [
    "http://digital.otago.ac.nz/terms.php"
  ],
  "rights_url": [
    "http://digital.otago.ac.nz/terms.php"
  ],
  "title": [
    "Gog and Magog, Stewart Island. 1880."
  ],
  "description": [
    "Lower left (l.l.) in ink: W. Deverell Jany 1880; margin below image in pencil: Gog &amp; Magog Stewart Island."
  ],
  "date": [

  ],
  "display_date": [

  ],
  "contributor": [

  ],
  "publisher": [

  ],
  "subject": [
    "Islands",
    "Landscape"
  ],
  "source": [
    "Found uncatalogued in Hocken 1947. Dr T.M. Hocken’s Collection"
  ],
  "creator": [
    "unknown"
  ],
  "dc_type": [
    "Image",
    "Still Image",
    "Watercolors",
    "Art"
  ],
  "format": [

  ],
  "category": [
    "Images"
  ],
  "landing_url": [
    "http://otago.ourheritage.ac.nz/items/show/4480"
  ],
  "internal_identifier": [
    "http://otago.ourheritage.ac.nz/items/show/4480"
  ],
  "large_thumbnail_url": [
    "http://s3.amazonaws.com/ourheritagemedia%2Ffullsize%2Fa24297b36e4d17d4b8bd4da8f7d6bb6c.jpg"
  ],
  "thumbnail_url": [
    "http://s3.amazonaws.com/ourheritagemedia%2Fsquare_thumbnails%2Fa24297b36e4d17d4b8bd4da8f7d6bb6c.jpg"
  ],
  "dc_identifier": [
    "oai:otago.ourheritage.ac.nz:4480"
  ],
  "source_id": "otago-hocken",
  "data_type": "record"
}
```

If you have a successful preview, close the modal window, scroll to the bottom of the page, enter some text in the message field (this is for parser version control), and click Update Parser Script.  Next, in the righthand columns, click on the name of your parser script under **History**, then click the blue button **Tag as Staging**.

You can now run a staging harvest, note that ‘Staging’ is a term bound to the Manager and does not relate to the Rails environment.

Click ‘Run Harvest’, and then click ‘Staging Harvest’, enter 50, and then click start.


Once the harvest is complete, it will take up to a minute for records to appear in solr, and the api response (indexing and solr auto-commit is set to every 60s)

You can now see your records on the API! Go to [http://localhost:3000/records.json?api_key=82HYSEI92N0DGN28](http://localhost:3000/records.json?api_key=82HYSEI92N0DGN28) to see the serialized JSON response.

You can also see them directly in solr at [http://localhost:8982/solr/#/development/query](http://localhost:8982/solr/#/development/query) and [http://localhost:8982/solr/development/select?q=*%3A*&wt=json&indent=true](http://localhost:8982/solr/#/development/query)

### What's next?

- Get started with harvesting by creating your first parser.

    [http://digitalnz.github.io/supplejack/manager/introduction-to-parser-scripts.html](http://digitalnz.github.io/supplejack/manager/introduction-to-parser-scripts.html)


- Know your Record Schema.

    This stack comes with a default [schema](https://github.com/DigitalNZ/supplejack_api_app/blob/master/app/supplejack_api/record_schema.rb) for you to get started.

    An example [parser](https://gist.github.com/hapiben/c904e581ea944b70533bb5fdf25efaa7) is also available to match the current Record Schema.
