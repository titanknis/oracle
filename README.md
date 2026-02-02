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
  oracle-ee:
    # image: oracle/database:19.3.0-ee  # Your locally built image
    image: container-registry.oracle.com/database/enterprise:latest
    container_name: oracle-ee
    environment:
      - ORACLE_PWD=oracle # SYS/SYSTEM password
      - ORACLE_CHARACTERSET=AL32UTF8 # Unicode support (critical for international apps)
      - ENABLE_ARCHIVELOG=true # Enable point-in-time recovery (EE feature)
    ports:
      - "1521:1521" # Default Oracle listener port
      - "5500:5500" # EM Express web console (Enterprise Manager)
    volumes:
      - oracle-ee-data:/opt/oracle/oradata # Persistent database files
      - oracle-ee-dbdump:/opt/oracle/dpdump # Data Pump export/import directory
        # This volume mounts your local SQL directory into the container
      - ~/Dev/oracle:/workspace
    shm_size: 2g # Critical: Oracle needs shared memory >1GB
    restart: unless-stopped
    deploy:
      resources:
        limits:
          memory: 8g # EE needs MINIMUM 4GB, 8GB recommended
          cpus: "2.0" # Minimum 2 CPU cores

volumes:
  oracle-ee-data:
  oracle-ee-dbdump:
```

---

### **Step 4: Start the Database**

Run this command in the same directory as your `compose.yml` file:

```bash
docker compose up -d
```

> **Note:** The first time you run this, it will download the Docker image, which may take a few minutes.

---

### wait till database finishes set up

```bash
docker logs -f oracle-ee
```

### set up password inside the container

```bash
docker exec -it oracle-ee bash;
sqlplus / as sysdba;
ALTER USER sys IDENTIFIED BY "oracle";
```

### **Step 5: Connect to the Database**

**Option A: If you installed SQLPlus (Step 2)**

```bash
# sqlplus system/oracle@localhost:1521/
sqlplus sys/oracle@//localhost:1521/orclpdb1 as sysdba
```

**Option B: Connect from inside the container (always works)**

```bash
docker exec -it oracle bash
sqlplus system/oracle
```
