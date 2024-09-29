# Instructions to test CDC in MySQL

## 1. Run docker container:
```bash 
docker-compose up -d
```

## 2. Ensure all your Docker containers are running:
```bash
docker-compose ps
```
You should see all services (zookeeper, kafka, mysql, and connect) in the "Up" state.

## 3. Connect to the MySQL database:
```bash
docker exec -it  mysql_cdc-mysql-1 mysql -u root -p
```
When prompted for a password, enter `debezium`.

## 4. Once connected to MySQL, switch to the `inventory` database:
```SQL
USE inventory;
```

## 5. Let's view the current contents of a table, say `customers`:
```SQL
SELECT * FROM customers;
```

## 6. Now, let's make some changes. Insert a new customer:
```SQL
INSERT INTO customers (first_name, last_name, email) 
VALUES ('John', 'Doe', 'john.doe@example.com');
```

## 7. Update an existing customer:
```SQL
UPDATE customers SET email = 'new.email@example.com' WHERE id = 1001;
```

## 8. Delete a customer:
```SQL
DELETE FROM customers WHERE id = 1002;
```

## 9. Now, let's check the Kafka topics to see the change events. First, list all topics:
```bash 
docker exec -it mysql_cdc-kafka-1 kafka-topics --list --bootstrap-server kafka:29092
``` 
You should see a topic named `dbserver1.inventory.customers` among others.

## 10. Let's consume messages from this topic to see the change events:
```bash
docker exec -it mysql_cdc-kafka-1 kafka-console-consumer --bootstrap-server kafka:29092 --topic dbserver1.inventory.customers --from-beginning
```
You should see JSON messages representing the changes you made, including the insert, update, and delete operations.