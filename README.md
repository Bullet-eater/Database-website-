<h2>Register</h2>
<form method="POST" class="form-box">
    <input type="email" name="email" placeholder="Enter Email" required><br><br>
    <input type="password" name="password" placeholder="Enter Password" required><br><br>
    <button type="submit" name="register">Register</button>
</form>
<p>Already have an account? <a href="?page=login">Login</a></p>
<h2>Login</h2>
<form method="POST" class="form-box">
    <input type="email" name="email" placeholder="Enter Email" required><br><br>
    <input type="password" name="password" placeholder="Enter Password" required><br><br>
    <button type="submit" name="login">Login</button>
</form>
<p>No account? <a href="?page=register">Register</a></p>
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
