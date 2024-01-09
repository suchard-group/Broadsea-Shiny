# Broadsea-Shiny
A Docker container based on rocker/shiny with added java jdk and additional R packages. The Shiny applications should be accessible on the Docker host in a sub-directory called "ShinyDeploy"  

## Configuration Steps

Below are steps to set up the docker environment, tested on an M1 Macbook pro. Further configurations may be required for other machines/OS. 

1. Fork from [`OHDSI/ShinyDeploy`](https://github.com/OHDSI/ShinyDeploy) and `git clone` to a local repo
2. `git clone` this repo to local
3. Create a simlink under the local `Broadsea-Shiny` directory to the `ShinyDeploy` directory:
   ```
   cd Broadsea-Shiny
   ln -s [PATH TO ShinyDeploy] .
   ```
4. Copy your local `postgresql` driver under the `ShinyDeploy` directory. You can also use the following `R` command to download the driver using the `DatabaseConnector` package:
   ```{r}
   DatabaseConnector::downloadJdbcDrivers(dbms = "postgresql", pathToDriver = [PATH TO ShinyDeploy])
   ```
5. Edit the `docker-compose.yml` file accordingly:
    * change `postgresql-42.2.18.jar` in the last line to the file name of your `postgresql` driver
    * the `platform: linux/amd64` flag seems to be required for an M1 Mac; you may not need it (or need another command) for a different machine/OS
6. Create a `.Renviron` file based on the `.Renviron.template`, something like:
   ```
   shinydbDatabase="shinydb"
   shinydbPort="5432"
   shinydbPw="[YOUR PASSWORD]"
   shinydbSchema="legend"
   shinydbServer="shinydb.cqnqzwtn5s1q.us-east-1.rds.amazonaws.com"
   shinydbUser="[YOUR USERNAME]"
   DATABASECONNECTOR_JAR_FOLDER=/home/shiny/
   ```
   Note that `[YOUR USERNAME]` and `[YOUR PASSWORD]` should be credentials that have read access to the relevant `shinydb` schemas on the backend of ShinyApps that you wish to run within this docker container.
7. Install `Docker` on your machine (or make sure that it is installed), and then run `docker compose up` to build and deploy the docker container
8. Go to your web browser and visit `localhost:80` to launch the ShinyApps
