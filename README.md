## TikTok Trends Storage and Management Application

This application fetches the latest TikTok trends by scrapping tiktok, stores them in a PostgreSQL database using prisma, and manages the data retrieval and update process using a FastAPI application to retrieve data and worker running independently to scrape and store new data. You will find a basic FastAPI application in `services/trends` that you will modify.

The worker code will be in `workers/tiktok`

No schema are imposed, you are free to come up with your own DB schema as long as you can justify your choices. Prisma is here to simplify your life, it comes with everything you need. Don;t over complicate things.

A docker-compose is provided so you don't have to install postgres on your machine and otehr dependencies.

If something isn't clear feel free to email at matthieu@mentium.io

We will evluate code clarity and good practices.

Bonuses aren't required but are highly encouraged, pick one of them.

## Challenge

- Update api code to generate a `openapi/api.cofilmai.yaml` file using FastAPI
- Uncomment swagger-ui service in the `docker-compose.yml` and make sure your swagger-ui is running properly
- Fetch and store trending data from TikTok using https://github.com/davidteather/TikTok-Api by country
  - The fetch should happen as a background tasks every X time.
- Extract Hashtags, Music, etc. from trending posts
- Expose endpoints to interact with TikTok trends data.
  - List trending posts by country, date or number of views
  - List trending hashtags by country

## Bonus
- Modify your code to fetch trending posts using a given topic or set of hashtags
- Add prisma studio in the docker-compose.yml file
- Come up with a feature of your own!

## Submission
- Fork this repo on your own github
- Once done push your code and email at matthieu@mentium.io

## Getting Started

### Prerequisites

- Python 3.10+
- Docker compose
- [Prisma](https://prisma-client-py.readthedocs.io/en/stable/)
- [Tikntok-Api](https://github.com/davidteather/TikTok-Api)

## Setup virtual environment

```sh
python3 -m venv .venv
source .venv/bin/activate
```

## Install requirements

```sh
pip install -r requirements.txt
```

## Start Docker
```sh
docker-compose up
```

## Generate Prisma Client

```sh
prisma generate
```

## Create new Prisma migration schema

Make sure your ddb is running in docker.
```sh
prisma migrate dev --name <migration_name>
```

## Apply migration
```sh
prisma migrate --dev
```

## Start api

```sh
cd services/trends
uvicorn main:app --reload
```

1. Create swagger doc with the command below:

```curl -0 localhost:8000/openapi.json >> openapi.json```

2. Pull and write trending data into DB using ensembledata's TikTok API:
    Endpoint: ```https://www.ensembledata.com/apis/tt/hashtag/posts```

3. Run trending data extracter script every hour:
    I would suggest to use more advanced tools like airflow to develop such kind of ETL pipelines.

    I tried setting up ```crontab```:

    1. Open cronjobs list.
        ```crontab -e```
    2. Add this command to the end.
        ```0 * * * * /path/to/project/cofilmai-challenge/venv/bin/python /path/to/project/cofilmai-challenge/services/trends/src/utils/save_trending_data.py```
    3. Save and close. ```CTRL+S```, ```CTRL+X```
    4. Check. ```crontab -l``` command should print out existing jobs list and your job should appear in the end.

    ```BUT: It seems cron job has some issues executing with asynchronous task```

    Then I found a way around with wrapping ```trends``` function with custom asynchronous ```scheduler```.
    As [here](/services/trends/src/utils/save_trending_data.py#L30)
