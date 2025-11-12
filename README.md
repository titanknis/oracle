### **Oracle Database Setup Guide using Docker**

This guide provides a simple way to run an Oracle database in a Docker container.

---

### **Step 1: Install Docker**

- **macOS:** Download and install **Docker Desktop** from [docker.com](https://www.docker.com/products/docker-desktop/).
- **Linux (Ubuntu):** Follow the official installation guide for Ubuntu: [docs.docker.com/engine/install/ubuntu/](https://docs.docker.com/engine/install/ubuntu/)

---

### **Step 2: (Optional) Install SQLPlus Locally**

This makes connecting to the database easier from your terminal.

- **macOS:**
  ```bash
  brew tap InstantClientTap/instantclient
  brew install instantclient-basic instantclient-sqlplus
  ```
- **Linux (Ubuntu):**
  Search online for "install sqlplus on Ubuntu" for up-to-date instructions.

---

### **Step 3: Project Setup**

1.  Create a directory for the project and navigate into it:
    ```bash
    mkdir -p ~/Dev/oracle
    cd ~/Dev/oracle
    ```
2.  Create a file named `compose.yml` in this directory with the following content:

    ```yaml
    services:
      oracle-db:
        # For Apple Silicon Macs OR modern systems, use the 21 version
        image: gvenzl/oracle-xe:21

        # For 64-bit Linux (x64), you can also use the 11 version if needed
        # image: gvenzl/oracle-xe:11

        container_name: oracle
        ports:
          - "1521:1521"
        environment:
          - ORACLE_PASSWORD=oracle
        volumes:
          # Mounts this folder into the container for file sharing
          - ./sql_scripts:/workspace
    ```

---

### **Step 4: Start the Database**

Run this command in the same directory as your `compose.yml` file:

```bash
docker compose up -d
```

> **Note:** The first time you run this, it will download the Docker image, which may take a few minutes.

---

### **Step 5: Connect to the Database**

**Option A: If you installed SQLPlus (Step 2)**

```bash
sqlplus system/oracle@localhost:1521
```

**Option B: Connect from inside the container (always works)**

```bash
docker exec -it oracle bash
sqlplus system/oracle
```
