You have run a stateless web application in Kubernetes. Your app stores its data in a database that is run in a separate database service. Your application code doesn't need to know the IP address for the database, which could be running anywhere in the cluster. The application only needs to know the service DNS name and port in order to connect to the database.

