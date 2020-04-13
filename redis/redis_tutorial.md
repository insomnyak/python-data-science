- [Installation](#installation)
- [Basic Operations](#basic-ops)
- [Expiration of Stored Values](#expiration)
- [Complex Data Structures](#data-structures)
- [Additional Resources](#resources)
- [Practice Redis](http://try.redis.io/)
- [Python Redis](https://pypi.org/project/redis/)

## <h2 id='installation'>Installation</h2>
[Install via Terminal](https://redis.io/download)
```bash
$ wget http://download.redis.io/releases/redis-5.0.8.tar.gz
$ tar xzf redis-5.0.8.tar.gz
$ cd redis-5.0.8
$ make

# The binaries that are now compiled are available in the src directory. Run Redis with:
$ src/redis-server

# interact with Redis using the built-in client:
$ src/redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```

```bash
# On this Mac

# run redis-server
$ redis-5.0.8/src/redis-server

# interact with redis
$ redis-5.0.8/src/redis-cli
```

## <h2 id='basic-ops'>Basic Operations</h2>
```bash
SET server:name "fido"
GET server:name # => returns "fido"
EXISTS server:name # => returns 1
EXISTS server:blabla # => returns 0

######
SET connections 10

# All operations in Redis are atomic

# stored value can be incremented
INCR connections # => 11
INCR connections # => 12
DEL connections
INCR connections # => 1

# stored value can be incremented by amount
INCRBY connections 100 # => 101

DECR connections # => 100
DECRBY connections 10 # => 90
```

## <h2 id='expiration'>Expiration of Stored Values</h2>
```bash
SET resource:lock "Redis Demo"
EXPIRE resource:lock 120 # in seconds

# show remaining seconds
TTL resource:lock # => 113
TTL resource:lock # => -2
# -2 > key doesn't exist
# -1 > key never expires, by default keys don't expire
# SET resets expiration time

# For milliseconds use:
# PEXPIRE and PTTL

# SET expiration
SET resource:lock "Redis Demo 3" EX 5
TTL resource:lock # => 5
# to remove expiration
PERSIST resource:lock
TTL resource:lock # => -1
```

## <h2 id='data-structures'>Complex Data Structures</h2>
- List (order is preserved)
    ```bash
    # Push Commands: create list if elements doesn't exist
    RPUSH # adds element to end of list
    LPUSH # adds element to start of list
    RPUSH friends "Alice"
    LPUSH friends "Bob"
    # PUSH commands are variadic, multiple elements can be added. Returns new length of list
    RPUSH friends 1 2 3 # => 6

    # Indexing, list items retrieval
    LRANGE # two indexes similar to python
    LRANGE friends 0 -1 # all elements 
        # => 1) "Sam", 2) "Alice", 3) "Bob"
    LRANGE friends 0 1 # => 1) "Sam", 2) "Alice"
    LRANGE friends 1 2 # => 1) "Alice", 2) "Bob"

    # Remove and Return List Element
    LPOP # removes/returns first element
    RPOP # removes/returns last element 
    LPOP friends # => "Sam"
    RPOP friends # => "3"

    # Get list length
    LLEN friends # => 4
    ```
- Set (unordered & unique)
    ```bash
    # Commands
    SADD # adds eleement. Returns 1 if succesful, 0 if item already exists
    SADD superpowers "flight" # >= 0
    SADD superpowers "x-ray vision" "reflexes"

    SREM # removes item. Return 1 if successful, 0 if item wasn't in set
    SREM superpowers "reflexes" # => 1
    SREM superpowers "making pizza" # => 0

    SISMEMBER # checks if element is in list
    SISMEMBER superpowers "flight" => 1
    SISMEMBER superpowers "reflexes" => 0

    SMEMBERS # returns list of all members of set
    SMEMBERS superpowers # => 1) "flight", 2) "x-ray vision"

    SUNION # combines two or more sets and returns list of all elements
    SADD birdpowers "pecking"
    SADD birdpowers "flight"
    SUNION superpowers birdpowers # => 1) "pecking", 2) "x-ray vision", 3) "flight"

    SPOP # removes/returns random # of elements from set
    SADD letters a b c d e f => 6
    SPOP letters 2 # => 1) "c" 2) "a"

    SRANDMEMBER # returns random member from list
    SRANDMEMBER letters 2 # => 1) "c" 2) "a"
    ```
- Sorted Sets
    ```bash
    # ZADD each elements is added with a score for sorting
    ZADD hackers 1940 "Alan Kay"
    ZADD hackers 1906 "Grace Hopper"
    ZADD hackers 1953 "Richard Stallman"
    ZADD hackers 1965 "Yukihiro Matsumoto"
    ZADD hackers 1916 "Claude Shannon"
    ZADD hackers 1969 "Linus Torvalds"
    ZADD hackers 1957 "Sophie Wilson"
    ZADD hackers 1912 "Alan Turing"

    # return range from sorted set
    ZRANGE hackers 2 4 # => 1) "Claude Shannon", 2) "Alan Kay", 3) "Richard Stallman"
    ```
- Hashes
    ```bash
    # hashes are map between string keys & values
    HSET user:1000 name "John Smith"
    HSET user:1000 email "john.smith@example.com"
    HSET user:1000 password "s3cret"

    # return hash
    HGETALL user:1000

    # set multiple fields at once
    HMSET user:1001 name "Mary Jones" password "hidden" email "mjones@example.com"

    # return one field
    HGET user:1001 name # => "Mary Jones"

    # Hash numerical values
    HSET user:1000 visits 10
    HINCRBY user:1000 visits 1 # => 11
    HINCRBY user:1000 visits 10 # => 21
    HDEL user:1000 visits
    HINCRBY user:1000 visits 1 # => 1
    ```
    - [all Hash commands](https://redis.io/commands#hash)

## <h2 id='resources'>Additional Resources</h2>
- [Redis Documentation](https://redis.io/documentation)
- [Command Reference](https://redis.io/commands)
- [Implement a Twitter Clone in Redis](https://redis.io/topics/twitter-clone)
- [Introduction to Redis Data Types](https://redis.io/topics/data-types-intro)
