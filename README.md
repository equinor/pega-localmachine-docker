# Docker for PEGA Personal Edition

## Files

PEGA PRPC Personal Edition zip file downloaded from PEGA website
  * Pega.com > Support > Download Pega Software > [Download Personal Edition](https://community1.pega.com/digital-delivery)


## Build Docker Image

### 1. Get the project

* Download / checkout this project to a suitable location in your hardrive and extract the contents. 

* Rest of the instructions below are w.r.t the current folder location.

### 2. Prepare for docker image build  of postgresql

* Extract the file pega.dump from PEGA-PE zip downloaded as mentioned in Files Prerequisite. For example, pega.dump can be located as **115148_PE_721.zip/data/pega.dump** in PEGA-PE 7.2.1. 

* Mention the location of pega.dump file in the file docker-compose.yml.

    ```yml
        volumes:
          - ${PEGA_DUMP}:/tmp/resources/  #substitue $  {PEGA_DUMP} with directory holding pega.dump file
    ```
    Instead of directly mentioning the location inside  docker-compose.yml, a neater way will be to use an   environment file. 
    Create a new file ".env" and add the location for   pega.dump inside "*.env"


    Content of .env file in my case is as below as i have   extracted pega.dump to /home/<user>/DockerBuild/  resources/pega.dump
    ```env
    PEGA_DUMP=/home/<user>/DockerBuild/resources/
    ```

* Pass username, password & db details to   docker-compose.yml file. These details will be used to    create the backend db, which will be used by PEGA-PE. 

    ```yml
    environment:
          - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}  #   substitute .with db password
          - POSTGRES_USER=${POSTGRES_USER}  # substitute    with db user
          - POSTGRES_DB=${POSTGRES_DB}  # substitute with   db name
    ```

    again, the neater way will be to just include these     too in the .env file
    ```env
    POSTGRES_PASSWORD=postgres
    POSTGRES_USER=postgres
    POSTGRES_DB=postgres
    PEGA_DUMP=/home/<user>/DockerBuild/resources/
    ```
    
    **Important: Eventhough this project supports use of    any username & password, the base image provided by    Pegasystems has hardcoded username "postgres" inside   one of the sql scripts. Because of this the username  will have to be "postgres".**
    
    **Important: POSTGRES_DB value has be "postgres" because    the contents in pega.dump file are specific to     database postgres.**


### 3. Prepare for docker image build  of pega web app

* Extract the file prweb.war from PEGA-PE zip downloaded as mentioned in Files Prerequisite. For example, these files can be located as **115148_PE_721.zip/PRPC_PE.jar/PersonalEdition.zip/tomcat/webapps** in PEGA-PE 7.2.1. 

* Place the files extracted on the previous step to **Project_Root/PegaPRPC-WebApp/resources**

### 4. Build docker images for postgresql & pega web app

* Run the below command to build the docker images for both postgresql & pega web app

    ```bash
    docker-compose build --no-cache
    ```
    This command will take some time to build the images based on your internet connection speed and your hardware specs. First time run will download  the required base images from internet. 

* Start **only postgresql container** because it on     the first run that databases are created and pega.dump  is restored back to postgresql

    ```bash
    docker-compose up pega-postgresql-backend
    ```
    This again will run for around 5-10mins. Make sure  that the default drive used by docker has atleast 5GB    of free space, as this step will restore pega.dump to  postgresql. 

    **Important: this image uses mount volume in the base image. Hence eventhough the volumes are not explicitly mentioned in docker-compose.yml, the data is persisted between restarts**

* Once the restore is complete, shutdown the docker instance by doing a Ctrl+C on the same terminal window that is running the docker instance or issuing the below command from a different terminal window
    ```bash
    docker-compose down pega-postgresql-backend
    ```

## Start & Stop docker containers
1. Issue the below command to start both pega web app & postgresql and send to background
    ```bash
    docker-compose up -d
    ```
2. Issue the below command to stop both pega web app & postgresql
    ```bash
    docker-compose stop
    ```
