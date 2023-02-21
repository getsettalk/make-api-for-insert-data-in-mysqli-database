# make-api-for-insert-data-in-mysqli-database

***Code to make API in PHP***
```php
<?php

// First, let's check the incoming request for the JWT bearer token. This should be included in the Authorization header.

$authHeader = $_SERVER['HTTP_AUTHORIZATION'];

if (preg_match('/Bearer\s(\S+)/', $authHeader, $matches)) {
    $token = $matches[1];

    // Now, let's decode and verify the token using a secret key.

    $secretKey = 'mySecretKey';
    $payload = jwt_decode($token, $secretKey, true);

    if ($payload === null) {
        // The token is invalid, so we should return a 401 Unauthorized response.
        http_response_code(401);
        die('Invalid token');
    }

    // The token is valid, so we can proceed with inserting the data into the database.

    // First, let's establish a connection to the MySQL database.

    $dbHost = 'localhost';
    $dbName = 'myDatabase';
    $dbUser = 'myUsername';
    $dbPass = 'myPassword';

    $conn = new mysqli($dbHost, $dbUser, $dbPass, $dbName);

    if ($conn->connect_error) {
        die('Connection failed: ' . $conn->connect_error);
    }

    // Next, let's prepare and execute the SQL statement to insert the data.

    $firstName = $_POST['firstName'];
    $lastName = $_POST['lastName'];
    $email = $_POST['email'];
    $phone = $_POST['phone'];

    $stmt = $conn->prepare('INSERT INTO contacts (first_name, last_name, email, phone) VALUES (?, ?, ?, ?)');
    $stmt->bind_param('ssss', $firstName, $lastName, $email, $phone);

    if (!$stmt->execute()) {
        // The data insertion failed, so we should return a 500 Internal Server Error response.
        http_response_code(500);
        die('Data insertion failed: ' . $stmt->error);
    }

    // The data insertion was successful, so we can return a 200 OK response.
    http_response_code(200);
    die('Data inserted successfully.');
} else {
    // The JWT bearer token was not provided, so we should return a 401 Unauthorized response.
    http_response_code(401);
    die('Bearer token not provided');
}
```


How to send request using CURL:
```
curl -X POST -H "Authorization: Bearer <your_secure_token_here>" -H "Content-Type: application/json" -d '{"name":"John Doe","phone":"123-456-7890"}' http://example.com/api/insert.php
```

