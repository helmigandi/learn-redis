# Data Structures

## Type

- Check key data type

```bash
type key
```

## List

- [List documentation commands](https://redis.io/docs/latest/commands/?group=list).

```bash
lpush usernames "rara21"
# (integer) 1

lpush usernames "shintanur01"
# (integer) 2

lpush usernames "rozakar90"
# (integer) 3

lpush usernames "rahmatz19"
# (integer) 4

lrange usernames 0 -1
# 1) "rahmatz19"
# 2) "rozakar90"
# 3) "shintanur01"
# 4) "rara21"

rpush usernames "nissaul8"
# (integer) 5

lrange usernames 0 -1
# 1) "rahmatz19"
# 2) "rozakar90"
# 3) "shintanur01"
# 4) "rara21"
# 5) "nissaul8"

rpop usernames 1
# 1) "nissaul8"

lrange usernames 0 -1
# 1) "rahmatz19"
# 2) "rozakar90"
# 3) "shintanur01"
# 4) "rara21"

lpop usernames 1
# 1) "rahmatz19"

lrange usernames 0 -1
# 1) "rozakar90"
# 2) "shintanur01"
# 3) "rara21"
```

## Set

- [Set documentation commands](https://redis.io/docs/latest/commands/?group=set).

```bash
sadd cities "jakarta" "bekasi" "bandung"
# (integer) 3

sadd cities "surabaya" "depok" "bandung"
# (integer) 2

smembers cities
# 1) "jakarta"
# 2) "bekasi"
# 3) "bandung"
# 4) "surabaya"
# 5) "depok"

srem cities "surabaya" "bekasi"
# (integer) 2

smembers cities
# 1) "jakarta"
# 2) "bandung"
# 3) "depok"
```

## Hashes

- Key-value pairs.
- [Hashes documentation commands](https://redis.io/docs/latest/commands/?group=hash).

```bash
hset "student:1" name "Abdul Rohmah" score 78 city "jakarta"
hset "student:2" name "Anissa Aulia" score 81 city "bekasi"
hset "student:3" name "Bambang Tri" score 95 city "bandung"
hset "student:4" name "Srikandi" score 68 city "depok"

hget student:1 name
# "Abdul Rohmah"

hgetall student:1
# 1) "name"
# 2) "Abdul Rohmah"
# 3) "score"
# 4) "78"
# 5) "city"
# 6) "jakarta"

hset "student:1" score 87

hget student:1 score
# "87"

hincrby "student:1" score -30
# (integer) 57

hincrby "student:1" score 27
# (integer) 84
```

## Sorted Sets

- Sorted data by score.
- [Sorted Sets documentation commands](https://redis.io/docs/latest/commands/?group=sorted-set).

```bash
zadd students_rank 98 "abdul rozak"
zadd students_rank 78 "ayu rahmadhani"
zadd students_rank 83 "shinta nur laila"
zadd students_rank 60 "andi"

zcard students_rank
# (integer) 4

zrange students_rank 0 -1
# 1) "andi"
# 2) "ayu rahmadhani"
# 3) "shinta nur laila"
# 4) "abdul rozak"

zrange students_rank 70 100 byscore
# 1) "ayu rahmadhani"
# 2) "shinta nur laila"
# 3) "abdul rozak"

zrem students_rank "andi"
# (integer) 1

zrange students_rank 0 -1 withscores
# 1) "ayu rahmadhani"
# 2) "78"
# 3) "shinta nur laila"
# 4) "83"
# 5) "abdul rozak"
# 6) "98"
```

## Stream

- Data will always added chronologically (append-only).
- [Stream documentation commands](https://redis.io/docs/latest/commands/?group=stream).

```bash
xadd application.log * level "info" message "User create product"
# "1712893370809-0"

xadd application.log * level "error" message "User fail to get product"
# "1712893377961-0"

xadd application.log * level "warning" message "User insert wrong value to product"
# "1712893382195-0"

xadd application.log * level "info" message "User create product"
# "1712893386545-0"

xread streams application.log 0
# 1) 1) "application.log"
#    2) 1) 1) "1712893370809-0"
#          2) 1) "level"
#             2) "info"
#             3) "message"
#             4) "User create product"
#       2) 1) "1712893377961-0"
#          2) 1) "level"
#             2) "error"
#             3) "message"
#             4) "User fail to get product"
#       3) 1) "1712893382195-0"
#          2) 1) "level"
#             2) "warning"
#             3) "message"
#             4) "User insert wrong value to product"
#       4) 1) "1712893386545-0"
#          2) 1) "level"
#             2) "info"
#             3) "message"
#             4) "User create product"
```

### Consumer Group

![Consumer Group](/images/consumer-group.png "Consumer Group")

- Make Stream data only read by 1 consumer.

```bash
# Create registration Stream with member as consumer group.
# $ is read last id and mkstream is create new.
xgroup create registration member $ mkstream

# Create two consumers.
xgroup createconsumer registration member member_1
xgroup createconsumer registration member member_2

# Insert data to Stream
xadd registration * userId 1
xadd registration * userId 2
xadd registration * userId 3
xadd registration * userId 4

xreadgroup group member member_1 count 1 block 0 streams registration >
xreadgroup group member member_2 count 1 block 0 streams registration >
# count 1: read only 1 data.
# block 0: wait forever until get the data (1000 is 1s).
# >: flag as unread data.

xreadgroup group member member_1 count 1 block 0 streams registration >
# 1) 1) "registration"
#    2) 1) 1) "1712895156116-0"
#          2) 1) "userId"
#             2) "1"

xreadgroup group member member_2 count 1 block 0 streams registration >
# 1) 1) "registration"
#    2) 1) 1) "1712895160118-0"
#          2) 1) "userId"
#             2) "2"

xreadgroup group member member_1 count 1 block 0 streams registration >
# 1) 1) "registration"
#    2) 1) 1) "1712895163743-0"
#          2) 1) "userId"
#             2) "3"

xreadgroup group member member_2 count 1 block 0 streams registration >
# 1) 1) "registration"
#    2) 1) 1) "1712895167725-0"
#          2) 1) "userId"
#             2) "4"
```

## Geospatial

- [Geo documentation commands](https://redis.io/docs/latest/commands/?group=geo).

```bash
geoadd mall_loc 106.9810826 -6.2278248 grand_mall
geoadd mall_loc 106.9885413 -6.248458 metropolitan_mall

geopos mall_loc grand_mall
geopos mall_loc grand_mall
# 1) 1) "106.98108404874801636"
#    2) "-6.22782382946217439"

geodist mall_loc grand_mall metropolitan_mall km
# "2.4388"

geodist mall_loc grand_mall metropolitan_mall m
# "2438.7582"

geosearch mall_loc fromlonlat 106.983295 -6.242475 byradius 2 km
# 1) "metropolitan_mall"
# 2) "grand_mall"
```

## HyperLogLog

- To save total amount of unique data only, we can not get the unique data.
- Example if you want to create unique customer counter.
- [HyperLogLog documentation commands](https://redis.io/docs/latest/commands/?group=hyperloglog).

```bash
pfadd visitor anton joko rozak abdul
pfadd visitor rani nissa ayu
pfadd visitor nissa aulia raka zein

pfcount visitor
# (integer) 10
```
