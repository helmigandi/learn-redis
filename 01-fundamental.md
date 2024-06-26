# Redis Fundamental

## Running Redis Server

```bash
redis-server redis.conf
```

## Running Redis CLI

```bash
redis-cli -h localhost -p 6379
```

## Select Database

```bash
# select database 0
select 0

# select database 1
select 1
```

## String

### Set

```bash
Set username_1 "bambang21"

# OK
```

### Get

```bash
get username_1

# "bambang21"
```

### Exists

```bash
exists username_1

# (integer) 1
```

### Append

```bash
append username_1 "tri"

# (integer) 12

get username_1

# "bambang21tri"
```

### Find (Not Recommended)

```bash
keys username_1

# 1) "username_1"
```

```bash
keys username*

# 1) "username_2"
# 2) "username_3"
# 3) "username_1"
```

```bash
keys *

# 1) "products"
# 2) "names"
# 3) "username_1"
```

### Delete

```bash
del username_1

# (integer) 1

get username_1

# (nil)
```

### Range

```bash
get username_2
# "rahmat2209"

setrange username_2 6 "1995"
# (integer) 10

get username_2
# "rahmat1995"

getrange username_2 6 -1
# "1995"
```

### Multiple Data

```bash
mset username_4 "shanti21" username_5 "rozak78"
# OK

mget username_4 username_5
# 1) "shanti21"
# 2) "rozak78"
```

## Expiration

```bash
expire username_2 5
# (integer) 1

# after 5 secs

get username_2
# (nil)

setex username_6 20 "uzumakianto52"
# 

# before 20 secs

ttl username_6
# (integer) 14

# after 20 secs

ttl username_6
# (integer) -2
```

## Increment & Decrement

```bash
set total_items "13"
# OK

incr total_items
# (integer) 14

decr total_items
# (integer) 13

incrby total_items 5
# (integer) 18

decrby total_items 3
# (integer) 15
```

## Flush

Remove all data in one database (`flushdb`) and remove all data in all database (`flushall`).

```bash
flushdb
# OK
```

## Pipeline

Save many data or bulk data to Redis.

- Create `username_list.txt` file

  ```txt
  setex username_7 180 "sasukeanbu007"
  setex username_8 180 "jokosusilo01"
  setex username_9 180 "lisanur90"
  setex username_10 180 "ayulestari33"
  ```

- Save command

  ```bash
  redis-cli -h localhost -p 6379 -n 0 --pipe < username_list.txt
  # All data transferred. Waiting for the last reply...
  # Last reply received from server.
  # errors: 0, replies: 4
  ```

  - `-n 0` is database number.

## Transaction

```bash
# Success

multi
# OK

setex product_1 180 "samsung"
# QUEUED

setex product_2 180 "sony"
# QUEUED

setex product_3 180 "polytron"
# QUEUED

exec
# 1) OK
# 2) OK
# 3) OK

mget product_1 product_2 product_3
# 1) "samsung"
# 2) "sony"
# 3) "polytron"

# Fail

multi
# OK

setex product_4 180 "apple"
# QUEUED

setex product_5 180 "lg"
# QUEUED

discard
# OK

mget product_4 product_5
# 1) (nil)
# 2) (nil)
```

## Monitor

```bash
monitor
# OK
# 1712718406.858241 [0 127.0.0.1:45636] "setex" "username_1" "180" "bambangnur21"
# 1712718427.252019 [0 127.0.0.1:45636] "setex" "username_2" "180" "uzumakirozak99"
```

## Server Information

```bash
info
# Server
# redis_version:7.2.4
# redis_git_sha1:00000000
# redis_git_dirty:0
# redis_build_id:d65a7e4dbe7bb1e4
# redis_mode:standalone
# os:Linux 6.5.0-26-generic x86_64
# arch_bits:64
```

```bash
config get databases
# 1) "databases"
# 2) "16"

config get bind
# 1) "bind"
# 2) "127.0.0.1 -::1"

config get save
# 1) "save"
# 2) "3600 1 300 100 60 10000"
```

## Client Connection

```bash
client list
# id=7 addr=127.0.0.1:59402 laddr=127.0.0.1:6379 fd=8 name= age=338 idle=0

client id
# (integer) 7

client kill 127.0.0.1:59402
# OK
```

## Protected Mode

- Default only `127.0.0.1`.
- Add more access by another connection in `redis.conf` with your IP address `bind 192.168.10.93`.
- To connect `redis-cli -h 192.168.10.93 -p 6379`

## Security

A good way to generate strong passwords is via the `ACL GENPASS` command.

- Add to `redis.conf`

  ```txt
  user default on +@connection
  user helmidev on +@all ~* &* >supersecret
  ```

  - default user is configured to be active (`on`) and get in connect only (`+@connection`),
  - `helmidev` user is configured to be active (`on`),
  - to access every possible key (`~*`),
  - Pub/Sub channel (`&*`),
  - Be able to call every possible command (`+@all`).

- Auth command

  ```bash
  auth helmidev supersecret
  ```

## Persistance

- Do not make Redis persistance
- Data will save in `dump.rdb`

```bash
save 3600 1 300 100 60 10000
#   * After 3600 seconds (an hour) if at least 1 change was performed
#   * After 300 seconds (5 minutes) if at least 100 changes were performed
#   * After 60 seconds if at least 10000 changes were performed
```

```bash
# save synchronous
save

# save asynchronous
bgsave
```

## Eviction

Remove data when database full.

The following policies are available:

- noeviction: New values aren’t saved when memory limit is reached. When a database uses replication, this applies to the primary database
- allkeys-lru: Keeps most recently used keys; removes least recently used (LRU) keys
- allkeys-lfu: Keeps frequently used keys; removes least frequently used (LFU) keys
- volatile-lru: Removes least recently used keys with the expire field set to true.
- volatile-lfu: Removes least frequently used keys with the expire field set to true.
- allkeys-random: Randomly removes keys to make space for the new data added.
- volatile-random: Randomly removes keys with expire field set to true.
- volatile-ttl: Removes keys with expire field set to true and the shortest remaining time-to-live (TTL) value.
