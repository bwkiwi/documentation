# Learn More

## What is GraphQL?

GraphQL is a powerful query language and runtime for APIs that provides a flexible, efficient way to access data. Unlike REST APIs, which require multiple endpoints to fetch different sets of data, GraphQL allows clients to request exactly what they need in a single query. This reduces network overhead, improves performance, and makes data retrieval more efficient.

At Monovandi, we use GraphQL to unify and access data collected from various IT monitoring and operational tools. Our platform aggregates this information without replacing existing tools, providing a structured and efficient way to retrieve insights. By leveraging GraphQL, we enable precise, real-time access to critical data while maintaining seamless integration with diverse enterprise environments.

## What are the benefits of GraphQL?

GraphQL offers several advantages over traditional API approaches:

- **Flexible Data Retrieval** – Clients can specify exactly which fields they need, reducing over-fetching (getting unnecessary data) and under-fetching (requiring additional requests to get needed data).
- **Single Endpoint** – Instead of managing multiple REST endpoints, GraphQL APIs expose a single endpoint that can handle complex queries.
- **Strongly Typed Schema** – GraphQL APIs are built on a defined schema, ensuring data consistency and predictability.
- **Real-Time Data** – With GraphQL subscriptions, clients can receive real-time updates when data changes.
- **Efficient Querying Across Relationships** – Clients can retrieve related data in a single request, even when it spans multiple entities.
- **Self-Documenting API** – GraphQL schemas are inherently self-documenting, making it easier for developers to explore and understand available data and operations.

## How Monovandi Uses GraphQL
At Monovandi, GraphQL is the core of our data integration platform. We leverage its capabilities to provide fast, structured, and meaningful access to IT monitoring and operational data. Our implementation of GraphQL offers several key advantages:

1. **Seamless Integration with Multiple Tools**
We connect to various enterprise monitoring, logging, and operational tools, allowing users to access and correlate data from different sources through a unified API. This eliminates the need to manually query separate systems, making data retrieval more efficient.

2. **Optimized Performance with Caching**
To ensure rapid response times, we implement intelligent caching at multiple levels. Frequently accessed data is cached, reducing the need to repeatedly fetch the same information from external sources. This results in near-instant responses for common queries.

3. **Intelligent Data Relationships**
Many IT tools operate in silos, making it difficult to correlate related data. Our GraphQL API builds relationships between objects that originate from different systems, allowing users to query across data sources in a structured way. For example, metrics from a monitoring tool can be linked to incidents from an alerting system, providing a complete picture of system health.

4. **Efficient Data Access for AI and Automation**
Our GraphQL interface is designed to support AI-driven automation and decision-making. It allows an LLM (Large Language Model) or other automated systems to request precise information without unnecessary data retrieval, making operations more efficient.

5. **Real-Time Insights**
By leveraging GraphQL subscriptions, we provide real-time updates when critical data changes. This is particularly useful for monitoring alerts, system performance trends, and other time-sensitive metrics.

## How do I use GraphQL?

In the most basic form, to make a request to our graphql endpoint, you simply make a POST request, passing the query string in the body of the request. 

### Coding examples

??? note "Show me some coding examples"
    ??? danger "Authentication"
        Access to data from the GraphQL API is protected and access can only be done with a valid token. 

        Only the **Login** and the **Alive** methods are open to unauthenticated requests. 
        
        Check the following section to find out how to Authenticate to access more interesting data. 

    === "Curl (unix)"

        ```console
            $ curl 'http://172.17.197.54:4000/graphql' \
            -H 'Content-Type: application/json' \
            -d '{"query":"query {  isAlive }"}'
        ```

        Response:
        ```console
            {
                "data": {
                    "isAlive": true
                }
            }
        ```

    === "Curl (Win)"

        ```console
            curl -X POST "http://172.17.197.54:4000/graphql" ^
            -H "Content-Type: application/json" ^
            --data "{\"query\": \"query { isAlive }\"}"
        ```

        Response:
        ```console
            {"data":{"isAlive":true}}
        ```

    === "python"

        ```console
            python -m pip install requests
            import requests, json
            body = {'query': 'query { isAlive }'}
            headers ={"Content-Type": "application/json"}
            response = requests.post("http://172.17.197.54:4000/graphql", data=json.dumps(body), headers=headers)
            response.json()
        ```

        Response:

        ```console
            {
                "data": {
                    "isAlive": true
                }
            }
        ```

??? note "Show me how I can authenticate"
    We use web tokens to be able to authenticate individual queries, which are passed in the header as the "Authorization" with the value set to "Bearer **token**>**"

    The token is obtained by making a request to the **login** mutation, passing in the user and password.

    In the following examples we will use the user **test@test.io** with the password **123456**

    === "Curl (unix)"

        To get the token:
        
        ```console
        curl -X POST \
        -H "Content-Type: application/json" \
        -d '{
            "query": "mutation Login{ login(user: "test@test.io", password: "123456") { token } }"
        }' http://172.17.197.54:4000/graphql
        ```

        Response:
        ```console
            {
                "data": {
                    "login": {
                        "token": "qyJhbGcio .... "
                    }
                }
            }
        ```

        To use it in a following request:
        ```console
        curl -X POST \
        -H "Content-Type: application/json" \
        -d '{
            "query": "query GetApplications {getApplications { nodes { name  } } }",
        }' http://172.17.197.54:4000/graphql
        ```

        Response:
        ```console
            {
                "data": {
                    "getApplications": {
                        nodes: [
                            {
                                "name": "Sport"
                            },
                            {
                                "name: "Entertainment"
                            },
                            {
                                "name: "News"
                            }
                            ...
                        ]
                    }
                }
            }
        ```


    === "Curl (Win)"
        ??? bug "Update pending - TODO"
            This section needs to be updated and tested based on the changes from the Curl (Unix) example. More details are coming soon.
        
        ```console
            curl -X POST "http://172.17.197.54:4000/graphql" ^
            -H "Content-Type: application/json" ^
            --data "{\"query\": \"query { isAlive }\"}"
        ```

        Response:
        ```console
            {"data":{"isAlive":true}}
        ```

    === "python"
        ??? bug "Update pending - TODO"
            This section needs to be updated and tested based on the changes from the Curl (Unix) example. More details are coming soon.

        ```console
            python -m pip install requests
            import requests, json
            body = {'query': 'query { isAlive }'}
            headers ={"Content-Type": "application/json"}
            response = requests.post("http://172.17.197.54:4000/graphql", data=json.dumps(body), headers=headers)
            response.json()
        ```

        Response:

        ```console
            {
                "data": {
                    "isAlive": true
                }
            }
        ```

??? note "Show me how to use variables"
    
    Within GraphQL you can use varibles with the queries and mutations, these can be either inline arguments or variable definitions. 
    
    ### Inline Arguments

    When using inline variables these are defined directly, for very simple queries or initial testing this might be useful. 
    In the example below the varaibles logonname and password are defined inline. 

    ```graphql title="Using Inline Variables" linenums="1" hl_lines="2"
        mutation Login {
            login(logonname: "test@test.io", password: "123456") {
                token
            }
        }
    ```

    

    ### Variable Definitions

    In GraphQL is is possible to define variables separately, and reference them in the query. 
    In the example below the variables logonname and password are used on the query, and the values are defined in the variable block

    ```graphql title="Using Separate Variables" linenums="1" hl_lines="1"
        mutation Login($loginLogonname: String!, $loginPassword: String!) {
            login(logonname: $loginLogonname, password: $loginPassword) {
                token
            }
        }
    ```

    ```json linenums="6" hl_lines="2-3"
        variables {
            "loginLogonname": "test@test.io",
            "loginPassword": "123456"
        }
    ```
    
    ??? info: The variables must be defined with the corresponding data type, in this case we have two strings
    ??? info: The varaible names used are flexible, but they must match (case sensitive) between the query definition (line 1), the usage (line 2) and variable definition (line 7/8).
 

    === "Curl (unix)"

        ```console
            $ curl 'http://172.17.197.54:4000/graphql' \
            -H 'Content-Type: application/json' \
            -d '{"query":"query {  isAlive }"}'
        ```

        Response:
        ```console
            {
                "data": {
                    "isAlive": true
                }
            }
        ```

    === "Curl (Win)"

        ```console
            curl -X POST "http://172.17.197.54:4000/graphql" ^
            -H "Content-Type: application/json" ^
            --data "{\"query\": \"query { isAlive }\"}"
        ```

        Response:
        ```console
            {"data":{"isAlive":true}}
        ```

    === "python"

        ```console
            python -m pip install requests
            import requests, json
            body = {'query': 'query { isAlive }'}
            headers ={"Content-Type": "application/json"}
            response = requests.post("http://172.17.197.54:4000/graphql", data=json.dumps(body), headers=headers)
            response.json()
        ```

        Response:

        ```console
            {
                "data": {
                    "isAlive": true
                }
            }
        ```

Or you can use the GraphQL client to discover what information is available and make queries on this

??? Show me how to access the from the Apollo Explorer

    The IT-OPS Graph is built on Apollo, this provides a built in explorer, which can be accessed via a standard browwer pointed to your API URL. 
    <div style="position: relative; padding-bottom: 58.971141781681304%; height: 0;"><iframe src="https://www.loom.com/embed/616fe1984ae2463c84ae566af636ef07?sid=d4b8f29c-ad1e-474e-a26a-4bf94bc416eb" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe></div>

If you want to learn about how GraphQL works, you can check out their website that goes over the language. <https://graphql.org/learn/>

Here, we will talk more about how to use GraphQL for the PeterPortal API. I recommend opening our [GraphQL playground](/graphql-playground) to test queries and get familiar. Please note that all of this information and much more is found in the `DOCS` tab in the playground.

## Tutorial

### Queries

Our API has 6 queries you can use to make a request for data. 

* `course`
* `instructor`
* `allCourses`
* `allInstructors`
* `schedule`
* `grades`

Each query returns an object of a certain type. For example, a `course` query will return a `Course` type, and an `allCourses` query will return a list of `Course` types. 

Based on the type, different fields can be specified. A `Course` type would have a field like `title` or `department`. By specifying which fields, the client will only receive those fields in the response. To find more about what fields each type has, check out the documentation in the playground. 

Some queries require arguments that will like our REST endpoints have parameters. This will help specify which course or instructor you want in a query. 

??? example "Example Query 1"
    
    In this example, the `course` query returns the `ID`, `title`, and `department` of the course `COMPSCI 161`, based on the argument passed.

    ``` graphql
    query {
      course(id:"COMPSCI161") {
        id
        title
        department
      }
    }
    
??? example "Example Query 2"
    Here is another example which returns sum of students who received As, Bs, Cs, Ds, Fs, and the average GPA of instructor "PATTIS, R." excluding P/NP.

    ``` graphql
    query {
      grades(excludePNP: true, instructor: "PATTIS, R.")
      {
        aggregate{
          sum_grade_a_count
          sum_grade_b_count
          sum_grade_c_count
          sum_grade_d_count
          sum_grade_f_count
          average_gpa
        }
      }
    }
    ```

### Nested Objects

One of the main features of GraphQL is being able to nest objects and fields inside a query. This will let the client get multiple resources in only one request. 

For example, a `Course` type will have a list of `CourseOffering` types, which are instances of the course in UCI's schedule of classes. `CourseOffering` types can also have nested types, like `Meeting`, which hold information about when a class meets. 

Almost all objects are linked to other objects in different ways. A `Course` will have multiple `Instructors` in its `instructor_history`, and an `Instructor` will have multiple `Courses` in its `course_history`.

??? example "Example"
    
    In this example, the `course` query requests a list of `Instructors` that taught the course, and all the `Courses` that these instructors taught as well.

    ``` graphql
    query { 
        course(id:"COMPSCI161") {
            instructor_history {
                name
                email
                course_history {
                    id
                    title
                }
            }
        }
    }
    ```

These are the basics of using GraphQL for the PeterPortal API. However, GraphQL really has so much more to offer, and if you're looking to learn more about this new technology, we highly recommend checking out their website, <https://graphql.org/>. There are links to so many more tutorials, examples and more.

## How we added GraphQL
To build our GraphQL endpoint, we used [GraphQL.js](https://graphql.org/graphql-js/), a node module you can use to create your own GraphQL API.