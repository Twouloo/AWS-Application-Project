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

A reflection report on if the application was to be deployed to a global scale can be read in this pdf: 
[CAB432 Report.pdf](https://github.com/Twouloo/AWS-Application-Project/files/14005335/CAB432.Report.pdf)


## Testing
Our approach to generating load was to send many requests to the Load balancer with very high frequency; Each request containing a unique query. This forces the node server to have to spend computational resources generating a new result for each request rather than fetching a response already stored in Redis or S3. One method used to do this was to create a python script that automates requests:


```
import requests
from concurrent.futures import ThreadPoolExecutor

# URL of the website
words_url = "https://www.mit.edu/~ecprice/wordlist.10000"
compressor_url = "http://..NAME...elb.amazonaws.com/api/getImages"

# Send a GET request to the URL
response = requests.get(words_url)

# Check if the request was successful (status code 200)
if response.status_code == 200:
    # Split the content into lines and extract the words
    words = response.text.split()

else:
    # Print an error message if the request was not successful
    print(f"Failed to retrieve content. Status code: {response.status_code}")

# Function to make a POST request
def make_post_request(word):
    compressed_data = requests.post(compressor_url, data={'query': word})
    if compressed_data.status_code == 200:
        print("Success - Retrieved compressed folder for: " + '"' + word);
    else:
        print("Error - Received status code of: " + str(compressed_data.status_code))

# Use ThreadPoolExecutor for parallel POST requests
with ThreadPoolExecutor(max_workers=1000) as executor:  # Adjust max_workers based on your preference
    # Submit the POST requests concurrently
    executor.map(make_post_request, words)
```
## Results 

It can be observed the application successfully scaled in response to load to optimize target response time. (Note extreme load was generated which resulted in > 98% CPU usage on most servers throughout testing).

![image](https://github.com/Twouloo/AWS-Application-Project/assets/150364814/03749f8b-5bc2-4024-b755-c8cf86125b4b)

### Average Response Time of each instance after creating load:
![image](https://github.com/Twouloo/AWS-Application-Project/assets/150364814/2a43803f-b8ca-4169-84d9-f9cbef1f2d19)

### Number of instances being scaled before, during and after creating load:

![image](https://github.com/Twouloo/AWS-Application-Project/assets/150364814/afba6c01-6910-4c0c-86e7-108920b44aeb)
