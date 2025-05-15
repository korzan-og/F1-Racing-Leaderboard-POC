#   F1 Live Leaderboard Application

This application provides a live view of an F1 racing leaderboard, along with detailed information about a selected driver. It uses a backend (Node.js with Express) to retrieve and process data from Confluent Cloud (Kafka) and a frontend (HTML/JavaScript) to display the information.

![](architecture.gif)

##  Table of Contents

* [Features](#features)
* [Technologies Used](#technologies-used)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Running the Application](#running-the-application)
* [Backend Setup](#backend-setup)
* [Frontend Setup ](#frontend-setup)
* [API Endpoints](#api-endpoints)
* [Confluent Cloud (Kafka) Integration](#confluent-cloud-kafka-integration)
* [Redis Usage](#redis-usage)
* [Graceful Shutdown](#graceful-shutdown)
* [Error Handling](#error-handling)


##  Features

* **Live Leaderboard:** Displays real-time F1 racing standings.
* **Driver Details:** Provides information on individual drivers, position in the lap, and interval.
* **Real-time Updates:** Leaderboard data is updated in real-time via Confluent Cloud (Kafka).
* **Backend API:** Provides a RESTful API for fetching leaderboard data.
* **Redis Caching:** Uses Redis to cache and serve leaderboard data efficiently.
* **User-Friendly Interface:** Clean and intuitive design with F1-themed styling.


##  Technologies Used

* **Backend:**
    * Node.js
    * Python
    * Redis
* **Frontend:**
    * HTML
    * CSS
    * JavaScript
* **Data Streaming:**
    * Confluent Cloud

##  Prerequisites

Before you begin, ensure you have the following installed and accounts set up:

* **Node.js and npm:** [https://nodejs.org/](https://nodejs.org/)
* **Python3**: [https://www.python.org/downloads/](https://www.python.org/downloads/)
* **Redis:** [https://redis.io/download](https://redis.io/download) (or accessible Redis instance)
* **Confluent Cloud Account:** You'll need an account on Confluent Cloud with a Kafka cluster set up. [https://www.confluent.io/confluent-cloud/tryfree](https://www.confluent.io/confluent-cloud/tryfree/)

##  Installation

1.  **Clone the repository:**

    ```bash
    git clone https://github.com/conflkrupa/F1-Racing-Leaderboard-POC.git
    cd F1-Racing-Leaderboard-POC
    ```

2.  **Install backend dependencies:**

    ```bash
    cd f1_backend
    npm install
    cd ..
    ```

##  Running the Application

1.  **Run the Producer code**
      
    * Sign in to Confluent Cloud- (https://confluent.cloud/auth_callback)
    * Create an Environment and a basic Cluster
    * Create an API key using service account in Accounts and Access(https://docs.confluent.io/cloud/current/security/authenticate/workload-identities/service-accounts/api-keys/overview.html) section(we will use it to authenticate to Confluent Cloud Cluster)
    * Go to topics in the left navigation pane, click topics
    * Create a topic with default configuration and name it "f1.leaderboard.results".
    * Open the f1_producer.py.
    * Add your Confluent Cloud Endpoint Url, API Key and API Secret in f1_producer.py as in the lines given below.

    ```javascript
    const KAFKA_BROKERS = '<CONFLUENT_CLOUD_BOOTSTRAP_SERVER_URL>'; // this is available in Cluster settings in the form of 'pkc-xxxx.region.provider.confluent.cloud:9092'
    const KAFKA_API_KEY = '<CONFLUENT_CLOUD_API_KEY>'; // this is available in the API keys section of Cluster overview once it is created.
    const KAFKA_API_SECRET = '<CONFLUENT_CLOUD_API_SECRET>';  // this is available in the API keys section of Cluster overview once it is created.
    const KAFKA_TOPIC = 'f1.leaderboard.results'; // The Kafka topic to consume from
    const KAFKA_GROUP_ID = 'your_kafka_consumer_group_id';
    ```

       * Run the following:
     ```bash
        python3 -m venv venv
        source venv/bin/activate
        pip3 install confluent-kafka
        python3 f1_producer.py
     ```
       
3.  **Start the backend server:**
      * The server will start at `http://localhost:9000/api/leaderboard`.
      * Open another new terminal and run

    ```bash
        cd f1_backend
        node server.js
     ```

    

4.  **Run the frontend:**
    * Open another new terminal
    * If you have `http-server` installed globally (`npm install -g http-server`), you can navigate to the frontend directory in your terminal and run `http-server` to serve the frontend at `http://localhost:8080`. Ensure this port is available and not consumed by any other service in your system.

##  Backend Setup (`server.js`)

The backend is built with Node.js and Express. It handles API requests, retrieves data from Redis, and consumes real-time data from Confluent Cloud (Kafka).

###  Configuration

* **Port:** The server runs on port `9000` by default. You can change this in `server.js`:

    ```javascript
    const PORT = 9000;
    ```

* **Redis:** The server connects to Redis using default settings for local instances. If you're using a remote Redis instance (e.g., cloud-hosted), update the connection details:

    ```javascript
    const redis = new Redis({ host: 'your_redis_host', port: your_redis_port });
    ```

* **Confluent Cloud (Kafka):** To connect to Confluent Cloud, you **must** update the following configuration in `server.js` with your Confluent Cloud cluster details:

    ```javascript
    const KAFKA_BROKERS = 'your_confluent_cloud_brokers'; // e.g., 'pkc-xxxx.region.provider.confluent.cloud:9092'
    const KAFKA_API_KEY = 'your_confluent_cloud_api_key';
    const KAFKA_API_SECRET = 'your_confluent_cloud_api_secret';
    const KAFKA_TOPIC = 'your_kafka_topic'; // The Kafka topic to consume from
    const KAFKA_GROUP_ID = 'your_kafka_consumer_group_id';
    ```

    * You can find these credentials in your Confluent Cloud cluster settings.

###  Dependencies

* `express`
* `cors`
* `ioredis`
* `node-rdkafka`

###  Key Functionality

* **Confluent Cloud (Kafka) Consumer:**
    * Connects to Confluent Cloud using the provided credentials.
    * Subscribes to the specified Kafka topic.
    * Consumes real-time leaderboard data messages.
    * Parses the JSON messages.
    * Updates the leaderboard data in Redis and to the frontend.
* **Redis Interaction:** Stores and retrieves leaderboard data from Redis. The key `f1_leaderboard_data` is used to store the data as a JSON string.
* **API Endpoint (`/api/leaderboard`):** Provides a GET endpoint to fetch the current leaderboard data from Redis.
* **CORS:** Enables Cross-Origin Resource Sharing to allow requests from the frontend.
* **Graceful Shutdown:** Handles signals to ensure proper disconnection from Confluent Cloud (Kafka) and Redis before exiting.
* **Error Handling:** Includes error logging and handling for Confluent Cloud (Kafka), Redis, and API requests.

##  Frontend Setup (`racing_leaderboard.html`)

   * The frontend consists of two pages that displays the live F1 leaderboard and driver information.
   * The index.html is the home page that asks user a question to choose the driver they would like to support.
   * racing_leaderboard.html consist of the code that displays leaderboard updates every few seconds and refreshes every 5 seconds.


###  Dependencies

* Tailwind CSS (CDN)
* Google Fonts (Inter, Orbitron)

###  Key Functionality

* **Data Fetching:** Fetches leaderboard data from the backend API (`/api/leaderboard`).
* **Data Rendering:** Dynamically generates the leaderboard table with the fetched data.
* **Driver Highlighting:** Highlights the selected driver in the table.
* **Clock Display:** Displays a real-time digital clock.
* **Loading Indicator:** Shows a loading spinner while fetching data.
* **Error Message Display:** Displays error messages to the user.
* **Back Button:** Provides a button to navigate back to a previous page.
* **Responsive Design:** Uses Tailwind CSS for a responsive layout.

##  API Endpoints

* `/api/leaderboard` (GET): Returns the current leaderboard data as a JSON array.


## Redis Usage
Redis acts as a high-speed, efficient layer between the backend server and the data source (Confluent Cloud Kafka). It significantly improves the application's performance, reduces the load on Kafka, and enhances the user experience by providing fast access to the leaderboard data.

* **Key:** The leaderboard data is stored under the key `f1_leaderboard_data`.
* **Data Format:** The data is stored as a JSON string.

## Graceful Shutdown
Here are the steps to shutdown the application.

* Stop running the producer.py, server.js and http-server by clicking cmd+c/ctrl-c to stop these programs.
* Go to Confluent Cloud and delete the topic "f1.leaderboard.results".

## Error Handling The application includes error handling to provide informative messages and prevent crashes.
But in case you face issues related to your messages not getting into redis, refer to the following solution.

### Problem: OPENSSL not available at build time error

**Solution:** Set `LDFLAGS` and `CPPFLAGS`

When you install OpenSSL with Homebrew on macOS, it's often installed in a location like `/usr/local/opt/openssl` (or `/opt/homebrew/opt/openssl@3` on Apple Silicon Macs), which isn't in the default search path for compilers.

1.  **Find the OpenSSL prefix:**

    ```bash
    brew --prefix openssl
    ```

    This command will output the path, for example, `/usr/local/opt/openssl@1.1` or `/opt/homebrew/opt/openssl@3` (the version might differ). Let's call this path `OPENSSL_PREFIX`.

2.  **Set environment variables:**

    ```bash
    export LDFLAGS="-L${OPENSSL_PREFIX}/lib"
    export CPPFLAGS="-I${OPENSSL_PREFIX}/include"
    ```

    This will create environment variables to use the openssl.

3.  **Reinstall `node-rdkafka`:**

    ```bash
    npm uninstall node-rdkafka # If it was previously installed without SSL support
    npm install node-rdkafka
    ```

    The `npm install` process for `node-rdkafka` should now pick up these environment variables and use them to find the Homebrew-installed OpenSSL libraries and headers, allowing it to compile with SSL support.
