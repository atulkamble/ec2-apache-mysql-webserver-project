<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My EC2 Project</title>
</head>
<body>
    <h1>Welcome to My EC2 Web Server!</h1>
    <p>This is a simple PHP application connected to MySQL.</p>

    <h2>Add User</h2>
    <form method="POST" action="">
        Name: <input type="text" name="name" required><br>
        Email: <input type="email" name="email" required><br>
        <input type="submit" name="submit" value="Add User">
    </form>

    <h2>User List</h2>
    <?php
    $servername = "localhost";
    $username = "myuser";
    $password = "mypassword";
    $dbname = "myproject";

    // Create connection
    $conn = new mysqli($servername, $username, $password, $dbname);

    // Check connection
    if ($conn->connect_error) {
        die("Connection failed: " . $conn->connect_error);
    }

    // Insert data
    if (isset($_POST['submit'])) {
        $name = $_POST['name'];
        $email = $_POST['email'];

        $sql = "INSERT INTO users (name, email) VALUES ('$name', '$email')";
        if ($conn->query($sql) === TRUE) {
            echo "<p>User added successfully!</p>";
        } else {
            echo "<p>Error: " . $sql . "<br>" . $conn->error . "</p>";
        }
    }

    // Fetch data
    $sql = "SELECT id, name, email FROM users";
    $result = $conn->query($sql);

    if ($result->num_rows > 0) {
        echo "<ul>";
        while ($row = $result->fetch_assoc()) {
            echo "<li>" . $row["name"] . " - " . $row["email"] . "</li>";
        }
        echo "</ul>";
    } else {
        echo "<p>No users found.</p>";
    }

    $conn->close();
    ?>
</body>
</html>
