# Week 2 Notes

## 2.1.1 - Data Lake

* Ingests all kind of data (both structured and unstructured)
* Raw, large and undefined (whereas data warehouses are refined, smaller and relational)
* Why do data lakes exist?
  * Cannot always define structure of data
  * Usefulness of data may be found later on
  * Cheaper storage of big data
* Common cloud providers are GCP (cloud storage), AWS (S3) and Azure (Azure Blob)

## 2.2.1 - Introduction to Workflow orchestration

* Process of scheduling and arranging tasks that form your data pipeline
* An orchestration tool allows you to schedlue, run and observe the entire process
* Prefect/Airflow most common
* An orchestration tool should allow you to orchestrate data workflows using a variety of tools

## 2.2.2- Introduction to Prefect Concepts

* We take a standard Python script with a main function that is executable, and then add orchestration to it using Prefect
* First step is to transform Python script into Prefect flow
`from prefect import flow` and using the `@flow` decorator for what happens inside the main function
* Tasks are also useful, can be used for the various functions that happen inside a flow. Can do things like try and run function multiple times, print logging, cache (e.g.  if extracting data then it doesn’t need to be done each time you try running the workflow, you can have a cache expiration)
  * Can then break the flow into multiple tasks (e.g. extract data, transform data, load data)
* A couple of other useful things with flows:
  * Can add parametrisation to a flow
  * Can add sub flows (flow within a flow – for example printing things inside a flow)
* Prefect also has a really good UI
  * `prefect orion start` - starts prefect UI. Need to add the API key to use prefect UI.
  * Tells you when things have been ran, when things failed, gives you notifications, limit how many tasks can run concurrency
* Prefect blocks – really useful in UI
  * Enable storage configuration, interface for interacting with external services
  * Utilise the sql alchemy connector block in prefect UI so you don’t need to write all the config info such as user, password, url etc. It then gives you the code that you need.

## 2.2.3 - ETL with GCP & Prefect

* To begin, start up prefect UI
* Working in the `flows` directory in the example.
* Saves NY taxi data in GCP using Prefect
* Goes through how to make a bucket in Google Cloud storage
* `prefect block register -m prefect_gcp` to register GCP block
* Can add GCP credentials too so that it isn't accessible to everyone


## 2.2.4 - From Google Cloud Storage to Big Query

* Moving the data from GCP to Big Query (last time it was from web to GCP)
* Purpose of this is that GCP is our data lake, and Big Query is our data warehouse -  the first video explained why data lakes are important in their own right
* Following the same structure of extract, transform and load - newest thing is using Big Query.
* Need to create a table in Big Query interface first.

## 2.2.5 - Parametrizing Flow & Deployments with ETL into GCS flow
* We want to pass arguments to the flow function at run time - in this example the taxi colour, year and month that we are getting the data from.
* The original flow was made into a sub flow, with a parent flow looping over different months and ingesting data for each month
* Prefect deployments: allow us to parametrize the flow, for example adding different arguments
* Two ways to build a deployment: through the CLI or through Python. Through the CLI use `prefect deployment`.
* To build a deployment, you need to specify the entry point and give it a name
* When the deployment builds, it creates a yaml file with the deployment configurations
* You can edit the parameters in this file to reflect what you want to use.
* After editing, you need to apply the deployment file so it knows the configuration
* Can see deployments in the prefect UI. Can change the parameters inside here.
* Can choose when you want it to run by sending a deployment to a work queue. An agent looks at the work queues and sees what needs to be ran. Trigger the agent in your CLI to make the work queues (and therefore deployments) run.
* You can create notifications if a specific run state (e.g. crashed, failed) happens - such as teams/slack webhooks.