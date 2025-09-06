<?php
session_start();

// Database connection
$host = "localhost";
$user = "root";
$pass = "";
$db = "shopdb";
$conn = new mysqli($host, $user, $pass, $db);

// Handle Registration
if (isset($_POST["register"])) {
    $email = $_POST["email"];
    $password = password_hash($_POST["password"], PASSWORD_BCRYPT);

    $stmt = $conn->prepare("INSERT INTO users (email, password) VALUES (?, ?)");
    $stmt->bind_param("ss", $email, $password);
    if ($stmt->execute()) {
        echo "<p style='color:green'>✅ Registration successful. Please <a href='?page=login'>login</a>.</p>";
    } else {
        echo "<p style='color:red'>⚠️ Error: " . $stmt->error . "</p>";
    }
}

// Handle Login
if (isset($_POST["login"])) {
    $email = $_POST["email"];
    $password = $_POST["password"];

    $stmt = $conn->prepare("SELECT id, password FROM users WHERE email=?");
    $stmt->bind_param("s", $email);
    $stmt->execute();
    $stmt->store_result();
    $stmt->bind_result($id, $hashed_password);
    $stmt->fetch();

    if ($stmt->num_rows > 0 && password_verify($password, $hashed_password)) {
        $_SESSION["user_id"] = $id;
        header("Location: ?page=shop");
        exit();
    } else {
        echo "<p style='color:red'>❌ Invalid email or password.</p>";
    }
}

// Handle Logout
if (isset($_GET["logout"])) {
    session_destroy();
    header("Location: ?page=login");
    exit();
}

// Default page
$page = isset($_GET["page"]) ? $_GET["page"] : "login";
?>

<!DOCTYPE html>
<html>
<head>
    <title>Phone & Laptop Shop</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 20px; }
        .form-box { border: 1px solid #ddd; padding: 15px; width: 300px; margin-bottom: 20px; }
        .product { border: 1px solid #ccc; padding: 10px; width: 200px; margin: 10px; display:inline-block; }
    </style>
</head>
<body>

<?php if ($page == "register"): ?>
    <h2>Register</h2>
    <form method="POST" class="form-box">
        <input type="email" name="email" placeholder="Enter Email" required><br><br>
        <input type="password" name="password" placeholder="Enter Password" required><br><br>
        <button type="submit" name="register">Register</button>
    </form>
    <p>Already have an account? <a href="?page=login">Login</a></p>

<?php elseif ($page == "login"): ?>
    <h2>Login</h2>
    <form method="POST" class="form-box">
        <input type="email" name="email" placeholder="Enter Email" required><br><br>
        <input type="password" name="password" placeholder="Enter Password" required><br><br>
        <button type="submit" name="login">Login</button>
    </form>
    <p>No account? <a href="?page=register">Register</a></p>

<?php elseif ($page == "shop" && isset($_SESSION["user_id"])): ?>
    <h2>Welcome to Our Shop</h2>
    <p><a href="?logout=1">Logout</a></p>
    <div>
        <?php
        $result = $conn->query("SELECT * FROM products");
        while($row = $result->fetch_assoc()): ?>
            <div class="product">
                <img src="<?= $row['image'] ?>" width="150"><br>
                <strong><?= $row['name'] ?></strong><br>
                <?= $row['description'] ?><br>
                <b>$<?= $row['price'] ?></b><br>
                <button>Add to Cart</button>
            </div>
        <?php endwhile; ?>
    </div>
<?php else: ?>
    <p>⚠️ Please <a href="?page=login">login</a> first.</p>
<?php endif; ?>

</body>
</html>
