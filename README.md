# Qdrant_Vector_Database
Data Processing with Vector Database

## Vector Databases

### What is a Vector Database?
A vector database is a database that stores vectors and allows you to perform vector operations on them. It is a type of database that is optimized for storing and querying vectors, which are mathematical objects that represent points in space. Vector databases are used in a variety of applications, including machine learning, natural language processing, and computer vision.

### Why use a Vector Database?
Short answer, they're fast. Vector databases are optimized for storing and querying vectors, which means that they can perform vector operations quickly and efficiently. This makes them ideal for applications that require fast access to large amounts of vector data, such as, recommendation systems.

### How does a Vector Database work?
A vector database works by storing vectors in a way that allows for efficient querying and retrieval. Vectors are typically stored in a high-dimensional space, which allows for complex operations to be performed on them. The database uses algorithms to index and search the vectors, which allows for fast retrieval of similar vectors.


## Qdrant

For this project, I used Qdrant as it was very simple to implement and work with. Qdrant is an open-source vector database that is optimized for storing and querying vectors. It is designed to be fast and efficient (as it is fully coded in Rust), making it ideal for applications that require fast access to large amounts of vector data.

### How to use Qdrant?
I followed the steps in this [GitHub repository](https://github.com/qdrant/qdrant?tab=readme-ov-file) to create and query a vector database. I will be going through each step in detail in this README as well for better understanding.

### Step 1: Using a pre-built Docker image
The easiest way to get started with Qdrant is to use a pre-built Docker image. You can run the following command in your terminal to pull the image from Docker Hub and start a Qdrant server:

```bash
docker pull qdrant/qdrant
```

Then, you can start the server by running the following command:

```bash
docker run -p 6333:6333 qdrant/qdrant
```

6333 is the default port for the Qdrant server. You can change it to any other port if you want, but to start, 6333 is a good call to make sure everything runs smoothly. 

### Step 2: Creating a collection
Once the server is up and running, you can create a collection by sending a POST request to the /collection endpoint. Open a new tab in your terminal and use the following curl command to create a collection named "my_collection":

```bash
curl -X PUT 'http://localhost:6333/collections/cocktails' \
    -H 'Content-Type: application/json' \
    --data-raw '{
        "vectors": {
          "size": 4,
          "distance": "Dot"
        }
    }'
```

The output should look something like this, which is indicating that the collection has been created successfully along with the time taken to create it: 
<img width="542" alt="image" src="https://github.com/AaryaDesai1/Qdrant_Vector_Database/assets/143753050/18ba8bec-97be-4680-ba1b-fb82a16c1e4e">

### Step 2.1: Ensuring the Collection was Created:
To ensure that the collection was created successfully, you can send a GET request to the /collections endpoint. Use the following curl command to list all the collections that have been created:

```bash
curl 'http://localhost:6333/collections/cocktails'
```

### Step 3: Inserting vectors
In the same terminal tab that you used to create the collection (i.e., Step 2), you can insert vectors into the collection by sending a POST request to the /collection/{collection_name}/upsert endpoint. Use the following curl command to insert a vector with the ID "1" and the values [1.0, 2.0, 3.0, 4.0] into the "my_collection" collection:

```bash
curl -L -X PUT 'http://localhost:6333/collections/cocktails/points?wait=true' \
    -H 'Content-Type: application/json' \
    --data-raw '{
        "points": [
          {"id": 1, "vector": [0.05, 0.61, 0.76, 0.74], "payload": {"name": "Margarita", "ingredients": ["tequila", "lime juice", "triple sec"], "recipe": "Mix all ingredients in a shaker with ice. Strain into a glass with a salted rim."}},
          {"id": 2, "vector": [0.19, 0.81, 0.75, 0.11], "payload": {"name": "Martini", "ingredients": ["gin", "vermouth", "olive"], "recipe": "Stir gin and vermouth with ice. Strain into a glass and garnish with an olive."}},
          {"id": 3, "vector": [0.36, 0.55, 0.47, 0.94], "payload": {"name": "Mojito", "ingredients": ["rum", "lime juice", "mint", "sugar", "soda water"], "recipe": "Muddle mint and sugar in a glass. Add rum, lime juice, and soda water. Stir and garnish with mint leaves."}},
          {"id": 4, "vector": [0.18, 0.01, 0.85, 0.80], "payload": {"name": "Cosmopolitan", "ingredients": ["vodka", "cranberry juice", "lime juice", "triple sec"], "recipe": "Shake all ingredients with ice. Strain into a glass and garnish with a lime twist."}},
          {"id": 5, "vector": [0.24, 0.18, 0.22, 0.44], "payload": {"name": "Old Fashioned", "ingredients": ["bourbon", "sugar", "bitters", "orange peel"], "recipe": "Muddle sugar and bitters in a glass. Add bourbon and ice. Stir and garnish with an orange peel."}},
          {"id": 6, "vector": [0.35, 0.08, 0.11, 0.44], "payload": {"name": "Negroni", "ingredients": ["gin", "vermouth", "campari"], "recipe": "Stir gin, vermouth, and campari with ice. Strain into a glass and garnish with an orange peel."}}
        ]
    }'
```
Though the following image is a bit difficult to understand, the last line shows that the values were successfully inserted into the collection.

<img width="1544" alt="image" src="https://github.com/AaryaDesai1/Qdrant_Vector_Database/assets/143753050/a0cd695b-611a-4961-b37c-aa25749cf0c5">

**What do each of those components mean?**
- `id`: The unique identifier for the vector.
- `vector`: The values of the vector.
- `payload`: Additional information associated with the vector. This is metadata that can be used to store additional information about the vector, and can store data types such as strings, integers, and arrays.

### Step 4.1: Querying the vector database
You can query the vector database to retrieve vectors that are similar to a given query vector. Use the following curl command to send a POST request to the /collection/{collection_name}/search endpoint to search for vectors that are similar to the query vector [1.0, 2.0, 3.0, 4.0]:

```bash
curl -X POST 'http://localhost:6333/collections/cocktails/points/search' \
    -H 'Content-Type: application/json' \
    --data-raw '{
        "vector": [0.35, 0.08, 0.11, 0.44],
        "top": 3
    }'
```
This will return the top 3 vectors that are most similar to the query vector. The output should look something like this:

<img width="618" alt="image" src="https://github.com/AaryaDesai1/Qdrant_Vector_Database/assets/143753050/8adaf7ae-9d13-4d44-89ef-413f55b6d910">

### Step 4.2: Adding a filter to the query
You can also add a filter to the query to retrieve vectors that match certain criteria. Use the following curl command to send a POST request to the /collection/{collection_name}/search endpoint with a filter to search for vectors that are similar to the query vector [1.0, 2.0, 3.0, 4.0] and have a payload field "name" that contains the string "Margarita":

```bash
curl -X POST 'http://localhost:6333/collections/cocktails/points/search' \
    -H 'Content-Type: application/json' \
    --data-raw '{
        "vector": [0.35, 0.08, 0.11, 0.44],
        "top": 3,
        "filters": [
            {
                "field": "payload.name",
                "operation": "eq",
                "value": "Margarita"
            }
        ]
    }'
```
This will return the top 3 vectors that are most similar to the query vector and have a payload field "name" that contains the string "Margarita". The output should look something like this:

<img width="608" alt="image" src="https://github.com/AaryaDesai1/Qdrant_Vector_Database/assets/143753050/cb226cfa-1e21-4400-b862-8c1779c86f0c">


### Step 5: Visualizing the vectors
To visualize the vectors, I needed to create a JSON file with the vectors and their metadata. I used the following Python script to create the JSON file:

```python
import json

# Define the points array
points = [
    {"id": 1, "vector": [0.05, 0.61, 0.76, 0.74], "payload": {"name": "Margarita", "ingredients": ["tequila", "lime juice", "triple sec"], "recipe": "Mix all ingredients in a shaker with ice. Strain into a glass with a salted rim."}},
    {"id": 2, "vector": [0.19, 0.81, 0.75, 0.11], "payload": {"name": "Martini", "ingredients": ["gin", "vermouth", "olive"], "recipe": "Stir gin and vermouth with ice. Strain into a glass and garnish with an olive."}},
    {"id": 3, "vector": [0.36, 0.55, 0.47, 0.94], "payload": {"name": "Mojito", "ingredients": ["rum", "lime juice", "mint", "sugar", "soda water"], "recipe": "Muddle mint and sugar in a glass. Add rum, lime juice, and soda water. Stir and garnish with mint leaves."}},
    {"id": 4, "vector": [0.18, 0.01, 0.85, 0.80], "payload": {"name": "Cosmopolitan", "ingredients": ["vodka", "cranberry juice", "lime juice", "triple sec"], "recipe": "Shake all ingredients with ice. Strain into a glass and garnish with a lime twist."}},
    {"id": 5, "vector": [0.24, 0.18, 0.22, 0.44], "payload": {"name": "Old Fashioned", "ingredients": ["bourbon", "sugar", "bitters", "orange peel"], "recipe": "Muddle sugar and bitters in a glass. Add bourbon and ice. Stir and garnish with an orange peel."}},
    {"id": 6, "vector": [0.35, 0.08, 0.11, 0.44], "payload": {"name": "Negroni", "ingredients": ["gin", "vermouth", "campari"], "recipe": "Stir gin, vermouth, and campari with ice. Strain into a glass and garnish with an orange peel."}}
]

# Save the points array as a JSON file
with open('vector_database.json', 'w') as json_file:
    json.dump(points, json_file)
```

This script creates a JSON file named "vector_database.json" with the vectors and their metadata. You can then use this JSON file to visualize the vectors in a tool like mpl_toolkits. 

Then, using more visualization code that is provided in this [notebook](https://github.com/AaryaDesai1/Qdrant_Vector_Database/blob/main/visualization.ipynb), I was able to:

### Visualize the vectors in 2D space.
<center><img width="1015" alt="image" src="https://github.com/AaryaDesai1/Qdrant_Vector_Database/assets/143753050/0a904746-2d07-4d2b-a638-132ab4ec1bf1"></center>


### Visualize the vectors in 3D space.
<center><img width="597" alt="image" src="https://github.com/AaryaDesai1/Qdrant_Vector_Database/assets/143753050/d3fb617a-e067-42a1-a179-22b7404ada49"></center>

