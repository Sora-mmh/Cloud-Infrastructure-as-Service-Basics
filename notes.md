# Managing DigitalOcean Droplets 

## 1. Creating a Droplet (DigitalOcean Virtual Server)

1. **Account Setup**  
   - Sign up on [DigitalOcean](https://cloud.digitalocean.com/registrations/new).  

2. **Droplet Creation Steps**  
   - In the DigitalOcean dashboard, select **Create > Droplets**.  
   - Select your preferred region (e.g., Frankfurt for proximity/latency).  
   - Choose the OS image: e.g., **Ubuntu 22.10 x64**.  
   - Pick Droplet size under **Basic** type, such as Regular CPU with 512 MB RAM (~$4/month).  
   - Under Authentication, select **SSH Key**: add your public key by pasting from local `~/.ssh/id_rsa.pub` (on macOS use `pbcopy  Firewalls**.  
   - Create a new firewall (e.g., named `droplet-firewall`).  
   - Add an **Inbound Rule** of type **SSH** restricting access to your machine's public IPv4 and IPv6 addresses.  
   - Use sites like [https://www.whatsmyip.org/](https://www.whatsmyip.org/) to find your IP.  
   - Apply this firewall to your Droplet under its **Droplets** tab.

4. **Connect via SSH**  
   ```bash
   ssh root@x.x.x.x
   ```

## 2. Installing Java on the Droplet

Update package lists and install headless Java runtime environment (OpenJDK 8):

```bash
apt update
apt install openjdk-8-jre-headless
```

## 3. Deploying and Running a Java Application on the Droplet

1. **Clone and Build Application Locally**  
   ```bash
   git clone https://github.com/nanuchi/java-react-example.git
   cd java-react-example
   ./gradlew build
   ```

2. **Transfer the Application JAR to the Droplet**  
   ```bash
   scp build/libs/java-react-example.jar root@x.x.x.x:/root
   ```

3. **Run the Application on the Droplet**  
   SSH into the server:  
   ```bash
   ssh root@x.x.x.x
   java -jar java-react-example.jar
   ```

4. **Open Application Port in Firewall**  
   - The app listens on TCP port 7071 by default.  
   - Add a firewall inbound rule on port 7071 for all IP addresses to allow external access.

5. **Access the Application**  
   Open:  
   ```
   http://:7071
   ```

6. **Run the Application in Background**  
   ```bash
   java -jar java-react-example.jar &
   ```
   Use to check process and network ports:  
   ```bash
   ps aux
   apt install net-tools      # (if netstat is missing)
   netstat -tlnp
   ```

## 4. Creating and Configuring a Non-Root User

**Security best practice: avoid working as root**

1. **Create a new user (example: `montassar`)**  
   ```bash
   adduser montassar
   ```

2. **Add the user to sudoers group**  
   ```bash
   usermod -aG sudo montassar
   ```
   Explanation:  
   - `-a` appends to supplementary groups  
   - `-G` specifies groups to add

3. **Switch to the new user**  
   ```bash
   su - montassar
   ```

4. **Enable SSH login for the new user**  
   - On your local machine, copy your public SSH key:  
     ```bash
     pbcopy 
     ```
   - Switch to new user:  
     ```bash
     su - montassar
     ```
   - Create SSH folder and authorized keys file:  
     ```bash
     mkdir -p ~/.ssh
     vim ~/.ssh/authorized_keys
     ```
   - Paste the copied public key, save and exit.  
   - Exit back to local shell.

5. **SSH using the new user**  
   ```bash
   ssh montassar@x.x.x.x
   ```

### PS

- Always restrict SSH firewall rules to trusted IPs to minimize unauthorized access risks.  
- Use SSH key authentication over password authentication.  
- Consider additional firewall rules for application ports and services as needed.  
- For production deployment, use process managers (e.g., `systemd`, `supervisord`) to keep your app running.
