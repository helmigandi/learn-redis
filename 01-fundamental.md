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
