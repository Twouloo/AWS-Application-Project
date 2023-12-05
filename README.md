# AWS-Application-Project

Demonstrates automatic load balancing of EC2 instances. 
* Computation involves performing compression on large quantities of high quality images to generate load on servers.
  * Health check threshold of 50% CPU usage was used as rule of when to scale.
  * AMI images using Ubuntu, Node and PM2 were used to replicate servers.
* Front-end hosted on S3 buckets.
* Redis Cache hosted as key-value pair storage using ElastiCache. Stores results for known queries as buffers to be returned when same query is called.
  * Query represents a unique input from a user that is a key phrase or word 
* S3 utilized as long term storage for key-value pairs.
* Architecture designed to be truely stateless.




![Web App Reference Architecture](https://github.com/Twouloo/AWS-Application-Project/assets/150364814/277dae05-2d4f-4b12-98de-61ba213bb28c)

