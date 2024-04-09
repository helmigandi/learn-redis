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
