## Steps with Commands

### **1. Create Data Directories**

Open Command Prompt and run:

```sh
mkdir D:\Danushka\Assignments\assignment_01\rs0-0
mkdir D:\Danushka\Assignments\assignment_01\rs0-1
mkdir D:\Danushka\Assignments\assignment_01\rs0-2
```


### **2. Start MongoDB Instances**

Open three separated Command Prompts. In each i have run (starts a MongoDB instance for the replica set on a different port and data directory)

```sh
mongod --replSet rs0 --port 27017 --dbpath D:\Danushka\Assignments\assignment_01\rs0-0 --bind_ip localhost
```

```sh
mongod --replSet rs0 --port 27018 --dbpath D:\Danushka\Assignments\assignment_01\rs0-1 --bind_ip localhost
```

```sh
mongod --replSet rs0 --port 27019 --dbpath D:\Danushka\Assignments\assignment_01\rs0-2 --bind_ip localhost
```



### **3. Connect to the First Instance**

Open a new Command Prompt and run:

```sh
mongosh --port 27017
```


### **4. Initiate the Replica Set**

In the MongoDB shell, (This command initializes the replica set and adds all three members)

```sh
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "localhost:27017" },
    { _id: 1, host: "localhost:27018" },
    { _id: 2, host: "localhost:27019" }
  ]
})
```


### **5. Check Replica Set Status**

In the shell, (This shows which node is PRIMARY and which are SECONDARY)

```sh
rs.status()
```



### **6. Simulate Failover**

- Stop the PRIMARY node by closing its Command Prompt window. (attached image 5,6)
- Wait a few seconds.(bcz it get few seconds to stop)
- In the shell connected to another node, run `rs.status()` again to see the new PRIMARY.
- Restart the stopped instance with its `mongod` command. It will rejoin as SECONDARY. 

---

## Issues Encountered and How to Resolve

### **Issue 1: `'mongod' is not recognized as an internal or external command`**

**Reason:** The MongoDB `bin` directory is not in the PATH variable.
**Solution:**

- Find the MongoDB `bin` folder (e.g., `C:\Program Files\MongoDB\Server\7.0\bin`).
- Add this path to the Windows PATH environment variable.
- Restarted Command Prompt.


### **Issue 2: `MongoServerError[NoReplicationEnabled]: This node was not started with replication enabled`**

**Reason:**This happened because MongoDB was running as a Windows service without the --replSet option. The service was started without replication enabled, so replica set commands did not work.

**Solution:**

- I needed to stop and remove the MongoDB Windows service, so I could start mongod manually with the --replSet option.

#### Commands I Used

```sh
net stop MongoDB
```

- This stopped the running MongoDB service.

```sh
sc delete MongoDB
```

### **Issue 3: Port Already in Use**

**Reason:** Another process is using the same port.
**Solution:**

- Use different ports for each instance (e.g., In my case I have used 27017, 27018, 27019).
- Make sure to close any previous `mongod` processes before starting new ones.

---

## How Replica Sets and Failover Work

A MongoDB replica set is a group of servers that keep the same data. One node is PRIMARY (handles all writes), and the others are SECONDARY (copy data from PRIMARY). If the PRIMARY fails, a SECONDARY is automatically elected as the new PRIMARY. When the original PRIMARY comes back, it rejoins as a SECONDARY.

---
