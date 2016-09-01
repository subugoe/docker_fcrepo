# docker_fcrepo


## prepare

	$> git clone https://github.com/subugoe/docker_fcrepo.git
	$> cd <project root: docker_fcrepo>

## configuration

Make a copy of the files ending with '.env_template' and set the properties in the new files ('.env'). Then...

	$> source .env
	$> ./start.sh

Actually only the Apache Karaf features fcrepo-indexing-solr and fcrepo-indexing-triplestore are activated. If you need fcrepo-reindexing, fcrepo-fixity, fcrepo-serialization or fcrepo-audit-triplestore comment-in the parts in fedora_camel_toolbox.sh and fedora_camel_toolbox.script (in docker/fcrepo/scripts/).

## build

	$> docker-compose build

## start

	$> docker-compose up -d


## logs

The startup of fcrepo service takes some time, so watch the fcrepo log: INFO [main] org.apache.catalina.startup.Catalina.start Server startup in 22707 ms

	$> docker-compose logs -f <service_name>


