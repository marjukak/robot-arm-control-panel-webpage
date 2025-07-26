Task: Web-Based Robot Arm Control Panel Using HTML, CSS, JavaScript, and PHP
For this task, I designed and implemented a fully functional robot arm control panel webpage that allows real-time interaction with six servo motors. The control panel is web-based, uses a MySQL database to store poses, and includes backend scripts in PHP to handle data transactions and updates.

Tools & Software I Used
 - XAMPP: My local development environment
    - Apache: To host PHP files
    - MySQL: For the database
    - phpMyAdmin: GUI to manage database tables
 - VS Code: To write and edit all project files (HTML, CSS, JS, PHP)
 - Google Chrome: To access http://localhost and interact with the web interface

Step 1: Design the Webpage (index.php)
 I started by creating the main interface using HTML, CSS, and JavaScript. The webpage contains:
  - Six sliders, each mapped to a servo motor (Motor 1 to Motor 6). The range is from 0 to 180 degrees.
  - Live value display next to each slider for easy reference.
  - Reset Button: Resets all sliders to 90 degrees.
  - Save Pose Button: Saves the current motor angles into the database.
  - Table below the sliders that shows all previously saved poses with:
      - Pose number
      - Motor angles for each servo
      - Action buttons: Load, Run, and Remove
The JavaScript handles:
  - Slider movements and real-time updates 
  - Sending AJAX requests to PHP scripts (for saving, loading, deleting poses)
The CSS styles the sliders and layout to be clean, minimal, and user-friendly.

Step 2: Create the Database Tables
In phpMyAdmin, I created a database named robot_arm_db and added a table poses with the following structure:

CREATE TABLE poses (
    id INT AUTO_INCREMENT PRIMARY KEY,
    motor1 INT NOT NULL,
    motor2 INT NOT NULL,
    motor3 INT NOT NULL,
    motor4 INT NOT NULL,
    motor5 INT NOT NULL,
    motor6 INT NOT NULL,
    status TINYINT(1) DEFAULT 1
);

 - Each row stores a full pose (the six motor values).
 - The status column is used to flag active or deleted poses. It defaults to 1 (active).
I also created a config.php file to manage database connection settings:

<?php
$host = "localhost";
$user = "root";
$password = "";
$database = "robot_arm_db";

$conn = new mysqli($host, $user, $password, $database);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>

Step 3: Create get_run_pose.php
This file handles the retrieval of pose data from the database. When I click "Run" on a pose, this script is triggered to fetch the pose details based on the selected ID and send the motor values back to the frontend.

Here's the general logic:

<?php
include 'config.php';

if (isset($_GET['id'])) {
    $id = intval($_GET['id']);
    $result = $conn->query("SELECT * FROM poses WHERE id = $id");
    
    if ($result->num_rows > 0) {
        $pose = $result->fetch_assoc();
        echo json_encode($pose);
    } else {
        echo json_encode(["error" => "Pose not found"]);
    }
}
?>

The response is in JSON, which the frontend parses and uses to update the sliders in real time.


Step 4: Create update_status.php
This script is responsible for soft-deleting a pose. When I click "Remove," it sends the pose ID to this script, which sets its status column to 0 (inactive). This allows me to keep the data without permanently deleting it.

<?php
include 'config.php';

if (isset($_POST['id'])) {
    $id = intval($_POST['id']);
    $conn->query("UPDATE poses SET status = 0 WHERE id = $id");
}
?>

The frontend listens for the success response and automatically removes the row from the table without needing to refresh the page.



Workflow Summary
 1. Launched XAMPP and enabled Apache and MySQL.
 2. Opened phpMyAdmin, created the robot_arm_db, and the poses table.
 3. Built the UI in index.php with all sliders and buttons.
 4. Used JavaScript (AJAX) to handle save/load/delete operations.
 5. Wrote PHP scripts:
       - get_run_pose.php: Fetches a pose from the DB.
       - update_status.php: Marks a pose as removed (sets status to 0).
       - config.php: Database connection handler.
 6. Tested everything by going to http://localhost/index.php.


Outcome
 I now have a working robot arm control interface where:
    - I can manually adjust each motor.
    - I can save, load, run, and remove poses.
    - Data is stored persistently in MySQL.
    - Everything updates smoothly in real time thanks to AJAX and PHP backend logic.
    
