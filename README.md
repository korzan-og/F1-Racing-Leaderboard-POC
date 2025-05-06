[EDIT] Readme for F1 app

#   F1 Live Leaderboard Application

This application provides a live view of an F1 racing leaderboard, along with detailed information about a selected driver. It uses a backend (Node.js with Express) to retrieve and process data from Confluent Cloud (Kafka) and a frontend (HTML/JavaScript) to display the information.

##   Table of Contents

* [Features](#features)
* [Technologies Used](#technologies-used)
* [Prerequisites](#prerequisites)
* [Installation](#installation)
* [Running the Application](#running-the-application)
* [Backend Setup](#backend-setup)
* [Frontend Setup](#frontend-setup)
* [API Endpoints](#api-endpoints)
* [Confluent Cloud (Kafka) Integration](#confluent-cloud-kafka-integration)
* [Redis Usage](#redis-usage)
* [Graceful Shutdown](#graceful-shutdown)
* [Error Handling](#error-handling)
* [Directory Structure](#directory-structure)
* [Configuration](#configuration)


##   Features

* **Live Leaderboard:** Displays real-time F1 racing standings.
* **Driver Details:** Provides information on individual drivers, position in the lap, and interval.
* **Real-time Updates:** Leaderboard data is updated in real-time via Confluent Cloud (Kafka).
* **Backend API:** Provides a RESTful API for fetching leaderboard data.
* **Redis Caching:** Uses Redis to cache and serve leaderboard data efficiently.
* **User-Friendly Interface:** Clean and intuitive design with F1-themed styling.
* **Error Handling:** Robust error handling for API requests and data processing.
* **Responsive Layout:** Designed to work on various screen sizes.

##   Technologies Used

* **Backend:**
    * Node.js
    * Python
* **Frontend:**
    * HTML
    * CSS
    * JavaScript
* **Data Streaming:**
    * Confluent Cloud

##   Prerequisites

Before you begin, ensure you have the following installed and accounts set up:

* **Node.js and npm:** [https://nodejs.org/](https://nodejs.org/)
* **Redis:** [https://redis.io/download](https://redis.io/download) (or accessible Redis instance)
* **Confluent Cloud Account:** You'll need an account on Confluent Cloud with a Kafka cluster set up. [https://www.confluent.io/confluent-cloud/tryfree/](https://www.confluent.io/confluent-cloud/tryfree/)

##   Installation

1.  **Clone the repository:**

    ```bash
    git clone [https://github.com/conflkrupa/F1-Racing-Leaderboard-POC/](https://github.com/conflkrupa/F1-Racing-Leaderboard-POC/)
    cd F1-Racing-Leaderboard-POC
    ```

2.  **Install backend dependencies:**

    ```bash
    cd f1-backend
    npm install
    cd ..
    ```

##   Running the Application

1.  **Start Redis Server:**

    * Ensure your Redis server is running and accessible. The default settings (host: `127.0.0.1`, port: `6379`) are assumed if running locally.  Otherwise, provide your Redis connection details.

2.  **Start the backend server:**

    ```bash
    cd f1-backend
    node server.js
    ```

    * The server will start at `http://localhost:9000/api/leaderboard`.

3.  **Open the frontend:**

    * Open the `racing_leaderboard.html` file (or the appropriate HTML file) in your web browser.
    * If you have `http-server` installed globally (`npm install -g http-server`), you can navigate to the frontend directory in your terminal and run `http-server` to serve the frontend at `http://localhost:8080`.

##   Backend Setup (`server.js`)

The backend is built with Node.js and Express. It handles API requests, retrieves data from Redis, and consumes real-time data from Confluent Cloud (Kafka).

###   Configuration

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

###   Dependencies

* `express`
* `cors`
* `ioredis`
* `node-rdkafka`

###   Key Functionality

* **Confluent Cloud (Kafka) Consumer:**
    * Connects to Confluent Cloud using the provided credentials.
    * Subscribes to the specified Kafka topic.
    * Consumes real-time leaderboard data messages.
    * Parses the JSON messages.
    * Updates the leaderboard data in Redis.
* **Redis Interaction:** Stores and retrieves leaderboard data from Redis. The key `f1_leaderboard_data` is used to store the data as a JSON string.
* **API Endpoint (`/api/leaderboard`):** Provides a GET endpoint to fetch the current leaderboard data from Redis.
* **CORS:** Enables Cross-Origin Resource Sharing to allow requests from the frontend.
* **Graceful Shutdown:** Handles signals to ensure proper disconnection from Confluent Cloud (Kafka) and Redis before exiting.
* **Error Handling:** Includes error logging and handling for Confluent Cloud (Kafka), Redis, and API requests.

##   Frontend Setup (`racing_leaderboard.html`)

The frontend is a single HTML page that displays the live F1 leaderboard and driver information.

###   Dependencies

* Tailwind CSS (CDN)
* Google Fonts (Inter, Orbitron)

###   Key Functionality

* **Data Fetching:** Fetches leaderboard data from the backend API (`/api/leaderboard`).
* **Data Rendering:** Dynamically generates the leaderboard table with the fetched data.
* **Driver Highlighting:** Highlights the selected driver in the table.
* **Clock Display:** Displays a real-time digital clock.
* **Loading Indicator:** Shows a loading spinner while fetching data.
* **Error Message Display:** Displays error messages to the user.
* **Back Button:** Provides a button to navigate back to a previous page.
* **Responsive Design:** Uses Tailwind CSS for a responsive layout.

##   API Endpoints

* `/api/leaderboard` (GET): Returns the current leaderboard data as a JSON array.

##   Confluent Cloud (Kafka) Integration

The backend is specifically designed to integrate with Confluent Cloud for real-time data streaming.

* **Configuration:** The Kafka consumer is configured to connect to Confluent Cloud using SASL authentication.  Ensure you provide the correct `KAFKA_BROKERS`, `KAFKA_API_KEY`, `KAFKA_API_SECRET`, `KAFKA_TOPIC`, and `KAFKA_GROUP_ID`.
* **Data Format:** The
 
