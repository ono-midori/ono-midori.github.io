# memcached

- [memcached](#memcached)
  - [Overview](#overview)
    - [Memcached](#memcached-1)
      - [How Does It Work?](#how-does-it-work)
      - [What is it Made Up Of?](#what-is-it-made-up-of)
    - [Design Philosophy](#design-philosophy)
      - [Simple Key/Value Store](#simple-keyvalue-store)
      - [Logic Half in Client, Half in Server](#logic-half-in-client-half-in-server)
      - [Servers are Disconnected From Each Other](#servers-are-disconnected-from-each-other)
      - [O(1)](#o1)
      - [Forgetting is a Feature](#forgetting-is-a-feature)
      - [Cache Invalidation](#cache-invalidation)
  - [TutorialCachingStory](#tutorialcachingstory)
    - [A Story of Caching](#a-story-of-caching)
  - [ConfiguringServer](#configuringserver)
    - [Further Information](#further-information)
    - [Commandline Arguments](#commandline-arguments)
    - [Init Scripts](#init-scripts)
    - [Multiple Instances](#multiple-instances)
    - [Networking](#networking)
      - [TCP](#tcp)
      - [UDP](#udp)
      - [Unix Sockets](#unix-sockets)
    - [Connection Limit](#connection-limit)
    - [Threading](#threading)
    - [Inspecting Running Configuration](#inspecting-running-configuration)
  - [ConfiguringClient](#configuringclient)
    - [Common Client Configurables](#common-client-configurables)
      - [Hashing](#hashing)
      - [Consistent Hashing](#consistent-hashing)
      - [Configuring Servers Consistently](#configuring-servers-consistently)
      - ["Weighting"](#weighting)
      - [Failure, or Failover](#failure-or-failover)
      - [Compression](#compression)
      - [Managing Connection Objects](#managing-connection-objects)
  - [Extstore](#extstore)
    - [External storage shim](#external-storage-shim)
    - [Quick Start](#quick-start)
      - [When to use this?](#when-to-use-this)
      - [How to use it?](#how-to-use-it)
      - [How to tune it?](#how-to-tune-it)
    - [Tuning guide](#tuning-guide)
      - [Why tune?](#why-tune)
      - [Primary tradeoffs](#primary-tradeoffs)
      - [Performance expectations](#performance-expectations)
      - [What to watch for](#what-to-watch-for)
      - [Page buckets](#page-buckets)
      - [Tuning options](#tuning-options)
      - [Runtime tunables](#runtime-tunables)
    - [Future plans](#future-plans)
    - [Rough notes](#rough-notes)
      - [Very high level detail](#very-high-level-detail)
      - [Performance](#performance)
      - [How do I use it?](#how-do-i-use-it)
      - [How safe is this really?](#how-safe-is-this-really)
    - [Technical Breakdown](#technical-breakdown)
      - [Goals](#goals)
      - [Assumptions](#assumptions)
      - [Architecture](#architecture)
        - [THREADS](#threads)
        - [STRUCTURES](#structures)
        - [THREADS](#threads-1)
        - [STRUCTURES](#structures-1)
        - [How an item is read from storage:](#how-an-item-is-read-from-storage)
        - [The lifetime of a storage page:](#the-lifetime-of-a-storage-page)
        - [How deletes, and overwrites work:](#how-deletes-and-overwrites-work)
        - [The compaction thread:](#the-compaction-thread)
        - [Memory thresholds:](#memory-thresholds)
  - [Restartable Cache](#restartable-cache)
    - [DETAILS](#details)
      - [DAX mounts and persistent memory](#dax-mounts-and-persistent-memory)
      - [CAVEATS](#caveats)
      - [FUTURE WORK](#future-work)
  - [Memcached API clients](#memcached-api-clients)
  - [Commands](#commands)
    - [Standard Protocol](#standard-protocol)
      - [No Reply](#no-reply)
      - [Storage Commands](#storage-commands)
        - [set](#set)
        - [add](#add)
        - [replace](#replace)
        - [append](#append)
        - [prepend](#prepend)
        - [cas](#cas)
      - [Retrieval Commands](#retrieval-commands)
        - [get](#get)
        - [gets](#gets)
      - [delete](#delete)
      - [incr/decr](#incrdecr)
      - [Statistics](#statistics)
        - [stats](#stats)
        - [stats items](#stats-items)
        - [stats slabs](#stats-slabs)
        - [stats sizes](#stats-sizes)
        - [flush_all](#flush_all)
  - [Memcached Text Protocol Meta Commands](#memcached-text-protocol-meta-commands)
    - [Command Basics](#command-basics)
    - [Replacing GET/GETS/TOUCH/GAT/GATS](#replacing-getgetstouchgatgats)
    - [New MetaData Flags](#new-metadata-flags)
    - [Atomic Stampeding Herd Handling](#atomic-stampeding-herd-handling)
    - [Early Recache](#early-recache)
    - [Serve Stale](#serve-stale)
    - [Pipelining Quiet mode with Opaque or Key](#pipelining-quiet-mode-with-opaque-or-key)
    - [Quiet mode semantics](#quiet-mode-semantics)
    - [Probabilistic Hot Cache](#probabilistic-hot-cache)
      - [Hot Key Cache Invalidation](#hot-key-cache-invalidation)
  - [CommonFeatures](#commonfeatures)
    - [Common Features](#common-features)
      - [Hashing](#hashing-1)
      - [Consistent Hashing](#consistent-hashing-1)
      - [Storing Binary Data or Strings](#storing-binary-data-or-strings)
      - [Serialization of Data Structures](#serialization-of-data-structures)
      - [Compression](#compression-1)
      - [Timeouts](#timeouts)
      - [Mutations](#mutations)
      - [Get](#get-1)
      - [Multi-Get](#multi-get)
    - [Less Common Features](#less-common-features)
      - [Get-By-Group-Key](#get-by-group-key)
      - [Noreply/Quiet](#noreplyquiet)
      - [Multi-Set](#multi-set)
  - [Programming](#programming)
    - [Basic Data Caching](#basic-data-caching)
      - [Initializing a Memcached Client](#initializing-a-memcached-client)
      - [Wrapping an SQL Query](#wrapping-an-sql-query)
      - [Wrapping Several Queries](#wrapping-several-queries)
      - [Wrapping Objects](#wrapping-objects)
      - [Fragment Caching](#fragment-caching)
    - [Extended Functions](#extended-functions)
      - [Proper Use of `add`](#proper-use-of-add)
      - [Proper Use of `incr` or `decr`](#proper-use-of-incr-or-decr)
    - [Cache Invalidation](#cache-invalidation-1)
      - [Expiration](#expiration)
      - [`delete`](#delete-1)
      - [`set`](#set-1)
      - [Invalidating by Tag](#invalidating-by-tag)
    - [Key Usage](#key-usage)
      - [Avoid User Input](#avoid-user-input)
      - [Short Keys](#short-keys)
      - [Informative Keys](#informative-keys)
  - [ProgrammingFAQ](#programmingfaq)
    - [Basics](#basics)
      - [How can you list all keys?](#how-can-you-list-all-keys)
        - [Why are you trying to dump the cache?](#why-are-you-trying-to-dump-the-cache)
        - [My application requires it](#my-application-requires-it)
      - [Why only RAM?](#why-only-ram)
      - [Why no complex operations?](#why-no-complex-operations)
      - [Why is memcached not recommended for sessions? Everyone does it!](#why-is-memcached-not-recommended-for-sessions-everyone-does-it)
      - [What about the MySQL query cache?](#what-about-the-mysql-query-cache)
      - [Is memcached atomic?](#is-memcached-atomic)
      - [Why is there a binary protocol?](#why-is-there-a-binary-protocol)
      - [How do I troubleshoot client timeouts?](#how-do-i-troubleshoot-client-timeouts)
    - [Setup Questions](#setup-questions)
      - [How do I authenticate?](#how-do-i-authenticate)
      - [How do you handle failover?](#how-do-you-handle-failover)
      - [How do you handle replication?](#how-do-you-handle-replication)
      - [Can you persist cache between restarts?](#can-you-persist-cache-between-restarts)
      - [Do clients and servers all need to talk to each other?](#do-clients-and-servers-all-need-to-talk-to-each-other)
    - [Monitoring](#monitoring)
      - [Why Isn't curr_items Decreasing When Items Expire?](#why-isnt-curr_items-decreasing-when-items-expire)
    - [Use Cases](#use-cases)
      - [When would you not want to use memcached?](#when-would-you-not-want-to-use-memcached)
      - [Why can't I use it as a database?](#why-cant-i-use-it-as-a-database)
      - [Can using memcached make my application slower?](#can-using-memcached-make-my-application-slower)
    - [Architectural](#architectural)
      - [Why can't we use memcached as a queue server?](#why-cant-we-use-memcached-as-a-queue-server)
  - [ProgrammingTricks](#programmingtricks)
    - [Namespacing](#namespacing)
      - [Simulating Namespaces with Key Prefixes](#simulating-namespaces-with-key-prefixes)
      - [Deleting By Namespace](#deleting-by-namespace)
      - [Storing sets or lists](#storing-sets-or-lists)
      - [Managing lists with `append/prepend`](#managing-lists-with-appendprepend)
      - [Zero byte values](#zero-byte-values)
      - [Reducing key size](#reducing-key-size)
      - [Accelerating counters safely](#accelerating-counters-safely)
      - [Rate limiting](#rate-limiting)
      - [Ghetto central locking](#ghetto-central-locking)
      - [Avoiding stampeding herd](#avoiding-stampeding-herd)
        - [Ghetto lock](#ghetto-lock)
        - [Outside mutex](#outside-mutex)
        - [Scaling expiration](#scaling-expiration)
        - [Gearmand or similar](#gearmand-or-similar)
      - [Ghetto replication](#ghetto-replication)
      - ["Touching" keys with add](#touching-keys-with-add)
  - [UserInternals](#userinternals)
    - [How Memory Gets Allocated For Items](#how-memory-gets-allocated-for-items)
    - [What Other Memory Is Used](#what-other-memory-is-used)
    - [When Memory Is Reclaimed](#when-memory-is-reclaimed)
    - [How Much Memory Will an Item Use](#how-much-memory-will-an-item-use)
    - [When Are Items Evicted](#when-are-items-evicted)
      - [How the LRU Decides What to Evict](#how-the-lru-decides-what-to-evict)
      - [libevent + Socket Scalability](#libevent--socket-scalability)
  - [ServerMaint](#servermaint)
    - [Watching Server Health](#watching-server-health)
      - [Issuing Commands](#issuing-commands)
      - [Important Stats](#important-stats)
        - [curr_connections](#curr_connections)
        - [listen_disabled_num](#listen_disabled_num)
        - [accepting_conns](#accepting_conns)
        - [limit_maxbytes](#limit_maxbytes)
        - [cmd_flush](#cmd_flush)
      - [`stats sizes`](#stats-sizes-1)
      - [Slab imbalance](#slab-imbalance)
      - [Troubleshooting Client Timeouts](#troubleshooting-client-timeouts)
    - [Stats for Application Health](#stats-for-application-health)
      - [Global hitrate](#global-hitrate)
      - [Hitrate per slab](#hitrate-per-slab)
      - [Evictions](#evictions)
      - [Looks Can be Deceiving](#looks-can-be-deceiving)
    - [OS Health (avoid swap!)](#os-health-avoid-swap)
    - [Upgrading](#upgrading)
  - [ClusterMaint](#clustermaint)
    - [Capacity Planning](#capacity-planning)
    - [Upgrades](#upgrades)
    - [Finding Outliers](#finding-outliers)
  - [Performance](#performance-1)
    - [General Performance](#general-performance)
      - [It Should Not Hang](#it-should-not-hang)
        - [OS Issues / Firewalls](#os-issues--firewalls)
      - [It Should Respond Quickly](#it-should-respond-quickly)
      - [Expiration Times Should Be Accurate](#expiration-times-should-be-accurate)
      - [How It Handles set Failures](#how-it-handles-set-failures)
    - [Theoretical Limits](#theoretical-limits)
      - [Max Clients](#max-clients)
      - [Maximum number of nodes in a cluster](#maximum-number-of-nodes-in-a-cluster)
        - [From the Client Perspective](#from-the-client-perspective)
        - [The Multiget Hole](#the-multiget-hole)
        - [A Well Designed Binary Protocol Client](#a-well-designed-binary-protocol-client)
        - [Remember Moore's Law](#remember-moores-law)
        - [Economy of Scale](#economy-of-scale)

## Overview

### Memcached

Free & open source, high-performance, distributed memory object caching system, generic in nature, but intended for use in speeding up dynamic web applications by alleviating database load.

Memcached is an in-memory key-value store for small arbitrary data (strings, objects) from results of database calls, API calls, or page rendering.

Memcached is simple yet powerful. Its simple design promotes quick deployment, ease of development, and solves many problems facing large data caches. Its API is available for most popular languages.


#### How Does It Work?

Memcached is a developer tool, not a "code accelerator", nor is it database middleware. If you're trying to set up an application you have downloaded or purchased to use memcached, read your app's documentation. This wiki and community will not be able to help you.

#### What is it Made Up Of?

- Client software, which is given a list of available memcached servers.
- A client-based hashing algorithm, which chooses a server based on the "key".
- Server software, which stores values with their keys into an internal hash table.
- LRU, which determine when to throw out old data (if out of memory), or reuse memory.


### Design Philosophy

#### Simple Key/Value Store

The server does not care what your data looks like. Items are made up of a key, an expiration time, optional flags, and raw data. It does not understand data structures; you must upload data that is pre-serialized. Some commands (incr/decr) may operate on the underlying data, but in a simple manner.

#### Logic Half in Client, Half in Server

A "memcached implementation" is partially in a client, and partially in a server. Clients understand how to choose which server to read or write to for an item, what to do when it cannot contact a server.

The servers understand how to store and fetch items. They also manage when to evict or reuse memory.

#### Servers are Disconnected From Each Other

Memcached servers are unaware of each other. There is no crosstalk, no syncronization, no broadcasting, no replication. Adding servers increases the available memory. Cache invalidation is simplified, as clients delete or overwrite data on the server which owns it directly.

#### O(1)

All commands are implemented to be as fast and lock-friendly as possible. This gives allows near-deterministic query speeds for all use cases.

Queries on slow machines should run in well under 1ms. High end servers can serve millions of keys per second in throughput.

#### Forgetting is a Feature

Memcached is, by default, a Least Recently Used cache. Items expire after a specified amount of time. Both of these are elegant solutions to many problems; Expire items after a minute to limit stale data being returned, or flush unused data in an effort to retain frequently requested information.

No "pauses" waiting for a garbage collector ensures low latency, and free space is lazily reclaimed.

See LRU documentation for more details on the latest algorithm.

#### Cache Invalidation

Rather than broadcasting changes to all available hosts, clients directly address the server holding the data to be invalidated.

## TutorialCachingStory

### A Story of Caching

*ed note*: this is an overview of basic memcached use case, and how memcached clients work

Two plucky adventurers, Programmer and Sysadmin, set out on a journey. Together they make websites. Websites with webservers and databases. Users from all over the Internet talk to the webservers and ask them to make pages for them. The webservers ask the databases for junk they need to make the pages. Programmer codes, Sysadmin adds webservers and database servers.

One day the Sysadmin realizes that their database is sick! It's spewing bile and red stuff all over! Sysadmin declares it has a fever, a load average of 20! Programmer asks Sysadmin, "well, what can we do?" Sysadmin says, "I heard about this great thing called memcached. It really helped livejournal!" "Okay, let's try it!" says the Programmer.

Our plucky Sysadmin eyes his webservers, of which he has six. He decides to use three of them to run the 'memcached' server. Sysadmin adds a gigabyte of ram to each webserver, and starts up memcached with a limit of 1 gigabyte each. So he has three memcached instances, each can hold up to 1 gigabyte of data. So the Programmer and the Sysadmin step back and behold their glorious memcached!

"So now what?" they say, "it's not DOING anything!" The memcacheds aren't talking to anything and they certainly don't have any data. And NOW their database has a load of 25!

Our adventurous Programmer grabs the pecl/memcache client library manual, which the plucky Sysadmin has helpfully installed on all SIX webservers. "Never fear!" he says. "I've got an idea!" He takes the IP addresses and port numbers of the THREE memcacheds and adds them to an array in php.

```perl
$MEMCACHE_SERVERS = array(
    "10.1.1.1", //web1
    "10.1.1.2", //web2
    "10.1.1.3", //web3
);
```

Then he makes an object, which he cleverly calls '$memcache'.

```perl
$memcache = new Memcache();
foreach($MEMCACHE_SERVERS as $server){
    $memcache->addServer ( $server );
}
```

Now Programmer thinks. He thinks and thinks and thinks. "I know!" he says. "There's this thing on the front page that runs SELECT * FROM hugetable WHERE timestamp > lastweek ORDER BY timestamp ASC LIMIT 50000; and it takes five seconds!" "Let's put it in memcached," he says. So he wraps his code for the SELECT and uses his $memcache object. His code asks:

Are the results of this select in memcache? If not, run the query, take the results, and PUT it in memcache! Like so:

```perl
$huge_data_for_front_page = $memcache->get("huge_data_for_front_page");
if($huge_data_for_front_page === false){
    $huge_data_for_front_page = array();
    $sql = "SELECT * FROM hugetable WHERE timestamp > lastweek ORDER BY timestamp ASC LIMIT 50000";
    $res = mysql_query($sql, $mysql_connection);
    while($rec = mysql_fetch_assoc($res)){
        $huge_data_for_front_page[] = $rec;
    }
    // cache for 10 minutes
    $memcache->set("huge_data_for_front_page", $huge_data_for_front_page, 0, 600);
}

// use $huge_data_for_front_page how you please
```

Programmer pushes code. Sysadmin sweats. BAM! DB load is down to 10! The website is pretty fast now. So now, the Sysadmin puzzles, "What the HELL just happened!?" "I put graphs on my memcacheds! I used cacti, and this is what I see! I see traffic to one memcached, but I made three :(." So, the Sysadmin quickly learns the ascii protocol and telnets to port 11211 on each memcached and asks it:

Hey, 'get huge_data_for_front_page' are you there?

The first memcached does not answer...

The second memcached does not answer...

The third memcached, however, spits back a huge glob of crap into his telnet session! There's the data! Only once memcached has the key that the Programmer cached!

Puzzled, he asks on the mailing list. They all respond in unison, "It's a distributed cache! That's what it does!" But what does that mean? Still confused, and a little scared for his life, the Sysadmin asks the Programmer to cache a few more things. "Let's see what happens. We're curious folk. We can figure this one out," says the Sysadmin.

"Well, there is another query that is not slow, but is run 100 times per second. Maybe that would help," says the Programmer. So he wraps that up like he did before. Sure enough, the server loads drops to 8!

So the Programmer codes more and more things get cached. He uses new techniques. "I found them on the list and the faq! What nice blokes," he says. The DB load drops; 7, 5, 3, 2, 1!

"Okay," says the Sysadmin, "let's try again." Now he looks at the graphs. ALL of the memcacheds are running! All of them are getting requests! This is great! They're all used!

So again, he takes keys that the Programmer uses and looks for them on his memcached servers. 'get this_key' 'get that_key' But each time he does this, he only finds each key on one memcached! Now WHY would you do this, he thinks? And he puzzles all night. That's silly! Don't you want the keys to be on all memcacheds?

"But wait", he thinks "I gave each memcached 1 gigabyte of memory, and that means, in total, I can cache three gigabytes of my database, instead of just ONE! Oh man, this is great," he thinks. "This'll save me a ton of cash. Brad Fitzpatrick, I love your ass!"

"But hmm, the next problem, and this one's a puzzler, this webserver right here, this one runing memcached it's old, it's sick and needs to be upgraded. But in order to do that I have to take it offline! What will happen to my poor memcache cluster? Eh, let's find out," he says, and he shuts down the box. Now he looks at his graphs. "Oh noes, the DB load, it's gone up in stride! The load isn't one, it's now two. Hmm, but still tolerable. All of the other memcacheds are still getting traffic. This ain't so bad. Just a few cache misses, and I'm almost done with my work. So he turns the machine back on, and puts memcached back to work. After a few minutes, the DB load drops again back down to 1, where it should always be.

"The cache restored itself! I get it now. If it's not available it just means a few of my requests get missed. But it's not enough to kill me. That's pretty sweet."

So, the Programmer and Sysadmin continue to build websites. They continue to cache. When they have questions, they ask the mailing list or read the faq again. They watch their graphs. And all live happily ever after.

Author: Dormando via IRC. Edited by Brian Moon for fun. Further fun editing by Emufarmers.

This story has been illustrated by the online comic [http://toblender.com/tag/memcached/ TOBlender.com].

## ConfiguringServer

### Further Information

See the `doc/protocol.txt` file within the tarball or on github for detailed information.

It's important that you look at the protocol.txt file from the version of memcached you run, as stats counters and commands are routinely updated.

### Commandline Arguments

Memcached comes equipped with basic documentation about its commandline arguments. View `memcached -h` or `man memcached` for up to date documentation. The service strives to have mostly sensible defaults.

When setting up memcached for the first time, you should pay attention to `-m`, `-d`, and `-v`.

`-m` tells memcached how much RAM to use for item storage (in megabytes). Note carefully that this isn't a global memory limit, so memcached will use a little more memory than you tell it to. Set this to safe values. Setting it to less than 64 megabytes may still use up to 64 megabytes as a minimum.

`-d` tells memcached to daemonize. If you're running from an init script you may not be setting this. If you're using memcached for the first time, it might be educational to start the service without `-d` and watching it.

`-v` controls verbosity to STDOUT/STDERR. Multiple `-v`'s increase verbosity. A single one prints extra startup information, and multiple will print increasingly verbose information about requests hitting memcached. If you're curious to see if a test script is doing what you expect it to, running memcached in the foreground with a few verbose switches is a good idea.

Most of the defaults are sensible. New features are often released as non-default options. Keep an eye on the ReleaseNotes for new options to try.

### Init Scripts

If you have installed memcached from your OS's package management system, odds are it already comes with an init script. They come with alternative methods to configure what startup options memcached receives. Such as via a `/etc/sysconfig/`memcached file. Make sure you check these before you run off editing init scripts or writing your own.

If you're building memcached yourself, the '`scripts/`' directory in the source tarball contains several examples of init scripts.

### Multiple Instances

Running multiple local instances of memcached is trivial. If you're maintaining a developer environment or a localhost test cluster, simply change the port it listens on, ie: `memcached -p 11212`.

### Networking

Since 1.5.6 memcached defaults to listening only on TCP. `-l` allows you to bind to specific interfaces or IP addresses. Memcached does not spend much, if any, effort in ensuring its defensibility from random internet connections. So you must not expose memcached directly to the internet, or otherwise any untrusted users. Using SASL authentication here helps, but should not be totally trusted.

#### TCP

`-p` changes where it will listen for TCP connections. When changing the port via -p, the port for UDP will follow suit.

#### UDP

`-U` modifies the UDP port, defaulting to off since 1.5.6. UDP is useful for fetching or setting small items, not as useful for manipulating large items. Setting this to 0 will disable it, if you're worried.

#### Unix Sockets

If you wish to restrict a daemon to be accessable by a single local user, or just don't wish to expose it via networking, a unix domain socket may be used. `-s <file>` is the parameter you're after. If enabling this, TCP/UDP will be disabled.

### Connection Limit

By default the max number of concurrent connections is set to 1024. Configuring this correctly is important. Extra connections to memcached may hang while waiting for slots to free up. You may detect if your instance has been running out of connections by issuing a `stats` command and looking at "listen_disabled_num". That value should be zero or close to zero.

Memcached can scale with a large number of connections very simply. The amount of memory overhead per connection is low (even lower if the connection is idle), so don't sweat setting it very high.

Lets say you have 5 webservers, each running apache. Each apache process has a MaxClients setting of 12. This means that the maximum number of concurrent connections you may receive is 5 x 12 (60). Always leave a few extra slots open if you can, for administrative tasks, adding more webservers, crons/scripts/etc.

### Threading

Threading is used to scale memcached across CPU's. The model is by "worker threads", meaning that each thread handles concurrent connections. Since using libevent allows good scalability with concurrent connections, each thread is able to handle many clients.

This is different from some webservers, such as apache, which use one process or one thread per active client connection. Since memcached is highly efficient, low numbers of threads are fine. In webserver land, it means it's more like nginx than apache.

By default 4 threads are allocated. Unless you are running memcached extremely hard, you should not set this number to be any higher. Setting it to very large values (80+) will make it run considerably slower.

### Inspecting Running Configuration

```shell
$ echo "stats settings" | nc localhost 11211
STAT maxbytes 67108864
STAT maxconns 1024
STAT tcpport 11211
STAT udpport 11211
STAT inter NULL
STAT verbosity 0
STAT oldest 0
STAT evictions on
STAT domain_socket NULL
STAT umask 700
STAT growth_factor 1.25
STAT chunk_size 48
STAT num_threads 4
STAT stat_key_prefix :
STAT detail_enabled no
STAT reqs_per_event 20
STAT cas_enabled yes
STAT tcp_backlog 1024
STAT binding_protocol auto-negotiate
STAT auth_enabled_sasl no
STAT item_size_max 1048576
END
```

cool huh? Between 'stats' and 'stats settings', you can double check that what you're telling memcached to do is what it's actually trying to do.

## ConfiguringClient

### Common Client Configurables

Most clients are similar in some important ways. They may be implemented differently, but they contain many common concepts.

#### Hashing

All clients support at least one method of "hashing" keys across servers. Keep in mind clients may not be compatible with each other. If you're using the perl Cache::Memcached and expect to resolve keys to servers the same way as a PHP client, it will not work.

There are exceptions to this, as clients based on [http://libmemcached.org libmemcached] should all have access to the same hasing algorithms.

#### Consistent Hashing

Consistent Hashing is a model that allows for more stable distribution of keys given addition or removal of servers. In a normal hashing algorithm, changing the number of servers can cause many keys to be remapped to different servers, causing huge sets of cache misses. Consistent Hashing describes methods for mapping keys to a list of servers, where adding or removing servers causes a very minimal shift in where keys map to.

With a normal hashing function, adding an eleventh server may cause 40%+ of your keys to suddenly point to different servers than normal.

However, with a consistent hashing algorithm, adding an eleventh server should cause less than 10% of your keys to be reassigned. In practice this will vary, but it certainly helps.

#### Configuring Servers Consistently

When adding servers to your configuration, pay attention that the list of servers you supply to your clients are exactly the same across the board.

If you have three webservers, and each webserver is also running a memcached instance, you may be tempted to address the "local" instance as "localhost". This will not work as expected, as the list of servers are now different between webservers. This means webserver 1 will map keys differently than server 2, causing mass hysteria among your users and business development staff.

The ordering is also important. Some clients will sort the server list you supply to them, but others will not. If you have servers "A, B, C", list them as "A, B, C" everywhere.

Use Puppet/Chef/rsync/whatever is necessary to ensure these files are in sync :)

#### "Weighting"

Given an imperfect world, sometimes you may have one memcached instance that has more RAM available than others. Some clients will allow you to apply more "weight" to the larger server. Others will allow you to specify one server multiple times to get it more chances of being selected.

Either way, you'd probably do well to verify that the "weighting" is doing what you expect it to do.

#### Failure, or Failover

What will your client do when a server is unavailable or provides an invalid response?

In the dark days of memcached, the default was to always "failover", by trying the next server in the list. That way if a server crashes, its keys will get reassigned to other instances and everything moves on happily.

However there're many ways to kill a machine. Sometimes they don't even like to stay dead. Given the scenario:

- Sysadmin Bob walks by Server B and knocks the ethernet cable out of its port.
- Server B's keys get "rerouted" to other instances.
- Sysadmin Bob is an attentive (if clumsy) fellow and dutifully restores the ethernet cable from its parted port.
- Server B's keys get "rerouted" back to itself.


Any updates you've made to your cache in the time it took Bob to realize his mistake have been lost, and old data is presented to the user. This gets even worse if:

- Server B's ethernet clip was broken by Bob's folly and hours later falls out of its port unattended.


Now your data has flipped back to very out of date data. Annoying.

Another erroneous client feature would actually amend the server list when a server goes out of commission, which ends up remapping far more keys than it should.

Modern life encourages the use of "Failure", when possible. That is, if the server you intend to fetch or store a cache entry to is unavailable, simply proceed as though it was a cache miss. You might still flap between old and new data if you have a Server B situation, but the effects are reduced.

#### Compression

Compressing large values is a great way to get more bang out of your memory buck. Compression can save a lot of memory for some values, and also potentially reduce latency as smaller values are quicker to fetch over the network.

Most clients support enabling or disabling compression by threshold of item size, and some on a per-item basis. Smaller items won't necessarily benefit as much from having their data reduced, and would simply waste CPU.

#### Managing Connection Objects

A common first-timer failure is that no matter what you do, you seem to run memcached flat out of connections. Your small server is allocating 50,000 connections to memcached and you have no idea what's going on.

Be wary of how you manage your connection objects! If you are constantly initializing connection objects every time you wish to contact memcached, odds are good you're going to leak connections.

Some clients (like PHP ones) have a less obvious approach to managing how many connections it will open. Continually calling 'addServer()' may just leak connections on you, even if you've already added a server. Read your clients' documentation to confirm what actions create connections and what will not.

## Extstore

### External storage shim

Feature for extending memcached's memory space onto flash (or similar) storage.

Built by default in 1.6.0 and higher.

For memcached 1.5.4 or higher. Requires `./configure --enable-extstore` during build time. Older versions do not have this feature.

### Quick Start

Extstore is an addition to memcached which leaves the hash table and keys in memory, but moves values to external storage (usually flash).

#### When to use this?

If you only have one or two memcached instances, you probably don't need this.

If stored values are much larger than keys and your hit rate would improve if you stored data for longer. You may save on cost (RAM can be expensive in "the cloud") if you have network capacity so spare.

There are useful tools online for analyzing memcached's stats output, but you can also simply telnet in and run `stats` and `stats slabs` and read the output. If you slab classes with large `chunk_size` values also have a relatively high number of `total_pages`, and you see evictions, you can give this a try.

You should also have flash storage. This will not work with rotational media.

#### How to use it?

Grab the latest memcached.

Build with: `./configure && make && make test && sudo make install` (use `./configure --enable-extstore` if your version is earlier than 1.6.0)

Use your normal startup options (-m -c, etc), and add:

`-o ext_path=/path/to/a/datafile:5G`

This initializes extstore with up to 5 gigabytes of storage. Storage is split internally into pages. By default pages have a size of 64 megabytes.

#### How to tune it?

Unless your servers are very busy, shouldn't do any further tuning. Try to monitor the output of `stats` and how much is being written to disk.

If you write too much to memcached, you may write too much to flash, which can burn out the drive or cause too much latency!

See below for a full description of tuning options.

### Tuning guide

Before tuning, you should have a good grasp of your operating system, how to monitor latency, disk utilization, CPU utilization, etc. You should have a graphing system setup to monitor trends over time, including memcached statistics. You should be able to monitor your hit ratio, and graph various `extstore_` stats via the `stats` command.

#### Why tune?

If you have a high capacity system (hundreds of gigs to terabytes), pushing hundreds of megabits to gigabits, you may see poor latency or poor hit rate. You may also see excessive writes to disk, which can age or burn out the device. Most server-quality flash devices are rated to be overwritten only 1-5+ times per day.

#### Primary tradeoffs

Items are flushed to flash storage from the tail of the LRU's, which are typically the least active items. The key and item structure (timestamps, flags, etc) plus 12 bytes for the flash location are kept in RAM. These are stored the same as normal items.

This means an item in slab class 50 using a hundred kilobytes of RAM would be flushed and reappear anywhere in slab class 1-6, depending on how long the key was.

If your values are only 60 bytes, you won't be saving much RAM (if any!).

Items are first written to a "write buffer" in RAM, which then gets flushed to a page. Once a page is full, it's capped and another one is then used.

Since items are pulled from the LRU tail, you are naturally less likely to hit the flash to retrieve them again later.

If you're writing new data to memcached faster than it can flush to flash, it will evict from the LRU tail rather than wait for the flash storage to flush. This is to prevent many failure scenarios as flash drives are prone to momentary hangs.

- extstore is explicitly not a levelled storage system, which is often used to manage write loads. In order to support a cache workload on flash media, more data needs to be kept in RAM, and accesses to flash need to be direct to minimize latency.

#### Performance expectations

- Many operations don't touch flash. IE: `touch`, `delete`, overwriting a value already on flash (including `add`), TTL expiration. They should be the same speed as without extstore.
- Only reading items that were flushed, writing items from the end of the LRU, and compacting existing flushed items, touch storage.
- In most deployments, items toward the end of the LRU are infrequently hit, but this can still be helpful if reducing misses has a large impact on hit ratio.
- You may only want to flush very large items (64k+), which frees up the most RAM, and are very easy for a flash device to saturate a network with. The smaller the items stored on flash, the more likely you are to saturate the flash device before your network device.

#### What to watch for

- `evictions` can mean you're out of RAM or you're writing too fast and extstore cannot keep up.
- very low age (via `stats items`) in higher slab classes, may mean too much memory is in use for small items. You can use less flash storage, shorten keys, or add RAM to the server.
- too much writing to disk. disk latency spikes. disk hangs. there aren't any internal measurements of disk latency as of this writing.
- too much writing can be alleviated by adding more RAM and tuning compaction settings as per below. Recently set objects are the most likely to be re-written or deleted, so the longer they can be kept in RAM the less writing you end up doing on flash.

#### Page buckets

Storage "pages" are split into different "buckets", which allows extstore to colocate items. Currently there are buckets for:

- Default, all non-special items end up here.
- Large items (> 512k, or larger if the item size max is increased).
- Compacted items (rewritten items are stored together to reduce fragmentation)
- "low" ttl items. This bucket isn't used by default. Pages used by the low bucket are ignored by compaction, which can reduce writes to disk. By storing these separately from objects with a longer TTL, fragmentation can be reduced significantly.

#### Tuning options

(see --help or `stats settings` for the latest defaults. Version 1.5.4 may not contain all defaults in these outputs yet.

- `ext_page_size` defaults to 64M. This shouldn't need to change unless you are storing very large values (multiple megabytes).
- `ext_wbuf_size` defaults to 8M. One write buffer is needed per bucket, By default 32 megabytes of memory are reserved for write buffers. This cannot be larger than the page size. Larger write buffers can help with write rate performance.
- `ext_threads` defaults to 1. If you have a high read latency but the drive is idle, you can increase this number. Stick to low values; no more than 8 threads.
- `ext_item_size` defaults to 512 bytes. Items larger than this can be flushed. You can lower this value if you want to save a little extra RAM and your keys are short. You can also raise this value if you only wish to flush very large objects, which is a good place to start.
- `ext_low_ttl` TTL's lower than this value will be colocated on disk. Off by default. Useful if you have a mix of high and low TTL's being used. Extstore has to compact or outright evict pages once flash is full. Mixing high and low TTL objects in a page will mean a page gets partially empty after some amount of time.

For example, if you only store objects with TTL's of 86400 (about a day) or 2400s. If you set `ext_low_ttl` to 2400 or higher, all of the latter objects will get stuck together. Once all of those objets have expired, the page they were in is reclaimed entirely. This avoids holes and compaction necessary if placed alongside the longer TTL'ed objects.

This `option` may change in the future; even more buckets may be useful.

- `ext_max_frag` A compaction tuning flag. If there are few free pages remaining, pages which are at least this empty will be rewritten to reclaim space. IE: ext_max_frag=0.5 will rewrite pages which are at least half empty. If no pages are half empty, the oldest page will be evicted.
- `ext_drop_unread` A compaction tuning flag. If enabled, and no pages match the ext_max_frag filter, a compaction will be done anyway. However, only items which are new or frequently accessed (ie; not in the COLD LRU) will be rewritten. This can cause more writing than simply evicting, but will keep active items around and raise hit ratio.
- `ext_compact_under` A compaction tuning setting. If fewer than this amount of pages are free, check the ext_max_frag filter and start compacting. Defaults to 1/4th of the pages.
- `ext_drop_under` A compaction tuning setting. If `ext_drop_unread` is enabled, and fewer than this many pages are free, compact and drop COLD data. This defaults to the same value as `ext_compact_under`.
- `ext_recache_rate` If an item stored on flash has been accessed more than once in the last minute, it has a one in N chance of being recached into RAM and removed from flash. Defaults to 2000. It's good to keep this value high; recaches into RAM cause fragmentation on disk, and it's rare for objects in flash to become frequently accessed. If they do, they will eventually be recached.

#### Runtime tunables

Many of the values above can be adjusted at runtime.

Via a socket to memcached: `extstore [command] [optional value]`. As of this writing, commands are:

```
item_size item_age low_ttl recache_rate compact_under drop_under max_frag drop_unread
```

The effects are the same as documented above.

### Future plans

TODO: Some info in the rough notes below, as well as doc/storage.txt.

### Rough notes
See doc/storage.txt for API and some implementation details. See memcached -h for start arguments.

Primary features:

- very high speed. minimal CPU overhead.
- writes are buffered and flushed sequentially. very good for improving read workload on most devices.
- keys and headers are left in memory, while data is written to disk. (see below)
- page compaction for reducing fragmentation over time.
- multikey reads can be batched to reduce latency from multiple swaps to IO threads.
- simple. uses a basic thread pool for IO's, suspends/resumes connections.
- does not block if items are being SET faster than they can be flushed to flash. objects are instead evicted from tail of memory to make space. (reliable, predictable speed)
- live control of what gets flushed to flash, ie: "objects >= 1024 bytes which are 3600s idle"

Tradeoffs:

- All data is tracked in memory. A restart of memcached effectively empties flash.
- Objects leave a small part in memory + the original key. This means very small objects don't get any benefit from being flushed.
- UDP is not be supported for objects flushed to disk.
- Not a levelled (or any kind of sophisticated) database. It wants to be fast and forget things, rather than efficiently use 100% of disk space. Expect 80-90% best case.

#### Very high level detail

A single file is split into N logical pages of Y size (64M default). Write buffers (8M+) are used to store objects later flushed into pages.

IO threads are used to asynchronously flush write buffers and read items back.

Items are left in the hash table along with their keys, with small (24 byte as of this writing) headers detailing where data lives in flash.

The storage engine recycles pages by changing their version number. Item headers remember both the page ID and version number at the time the object was flushed. An attempted read with the wrong version will result in a miss.

The system is most useful if you have a breakdown of different item sizes: pools with a mix of small and large, or if you can break off a small pool of machines with flash devices to store much larger items. Freeing up more memory on your small item pools can dramatically improve hitrates, or simply reduce costs overall.

#### Performance

This is designed for maximum performance. It unifies into the existing hash table data structure in order to avoid burning CPU from shifting items into a secondary system. This also greatly reduces the IOPS required to maintain data.

IE: Rewriting an object does not touch flash. It simply "forgets" the position of the previous item on disk and informs the engine that particular page has fewer bytes stored. The same exists for deletes.

Recycling pages also does not require writing to them.

Compaction however does require rewriting pages. More details of that is in doc/storage.txt

#### How do I use it?

`-o ext_path=/data/file:5G` this is enough to get you started. See `memcached --help` for other options. This wiki will update with more details on what the options do, but most users should be able to use a minimal subset of options. If you have a very high performance setup, feel free to reach out and we can walk you through a configuration.

The above creates an external cache of 5 gigabytes. Due to how the shim works, you wil not be able to actually store 5 gigabytes due to fragmentation over time. Background jobs will attempt to keep fragmentation in check.

#### How safe is this really?

I test pretty aggressively while developing features. The general stability of memcached should attest to a high quality. This feature is still in an experimental tages, but has had a good amount of baking time for the operations it does support. Keep in mind that many features are disabled for items sent to disk (incr/decr/append/prepend)

### Technical Breakdown

This section is a detail on the architecture and tradeoffs of external storage.

#### Goals

Primarily: keeping "cache patterns" in mind, get the most out of a storage device by minimizing the accesses to it.

- Larger values (typically 1k+) can use flash. Small values stay in RAM.
- A single read to serve a single key.
- Asynchronous batched writes to drive.
- Main hash table is authoritative: miss/delete/overwrite must not use the drive.
- Writing new items to cache must never block on the flash device.
  - Items are evicted from the LRU tail in RAM if extstore writer lags.
- Forgetting / evicting data from storage must not use the drive.


#### Assumptions

Some assumptions about access patterns have to be made in order to utilize flash as a cache medium. Writing is expensive (destructive to the drive), and caches typically have much higher churn rate than databases. Flash also increases the latency of each key fetched, which can make caching small values on flash ineffective compared a range fetch against a high end database.

- Longer TTL's for data that hits disk (and good reuse)
- Latency overhead for small items is high compared to benefit of storing them.
- Latency overhead for larger items is better than a miss (within reason)
- A minority of data is hot (highly accessed), with a long tail of colder data.

#### Architecture

A review of the main threads and data structures in memcached:

```
               THREADS            +            STRUCTURES
 | +----------------------+ |
 | ------------------------ ||         |     +----------+
+------+  +--v-----+ +--v-----+   |     |hash table|
|listen|  |worker 1| |worker 2|   |     +----------+
+------+  +--------+ +--------+   |
                        +--------->     +---+
                                  |     |LRU|
+--------------+                  |     +---+
|lru maintainer+------------------>
+--------------+                  |     +--------------+
                                  |     |slab allocator|
+-----------+                     |     +--------------+
|lru crawler+--------------------->
+-----------+                     |
                                  |
+---------------+                 |
|slab page mover+----------------->
+---------------+                 |
                                  +
```

##### THREADS

The listen thread accepts new connections, it then passes the socket to workers via writing a byte to a pipe. Each worker has its own "notifier pipe", which it receives events from via libevent polling. This mechanism is reused for extstore, allowing IO requests to pass to/from IO threads.

A client connection sticks to a worker thread for its lifetime. One worker thread per CPU is ideal.

The LRU maintainer thread moves items inbetween sub-LRU's (hot|warm|cold|etc). It also makes decisions on when to schedule page moves, or LRU crawls.

The LRU crawler reaps expired/invalid items, including items extstore invalidates.

Memory is divided into slab classes with fixed chunk sizes. Fixed size (1M) pages are assigned to each slab-class and divided into chunks as-needed. Over time, shifts in average item sizes or access patterns can "starve" a class. The page mover thread is able to safely re-assign pages between slab classes to avoid starvation.

Misc. threads: hash-table-expander, logger. These do not interact with extstore.

##### STRUCTURES

- Hash table is chain bucketed. "next" is embedded in item memory.
- LRU is doubly-linked, with next/prev embedded in item memory.
- Each slab class has its own LRU.
- Each LRU is split into HOT|WARM|COLD|TEMP


Now, with extstore:

```

               THREADS            +            STRUCTURES
 | +----------------------+ |
 | ------------------------ ||         |     +----------+
+------+  +--v-----+ +--v-----+   |     |hash table|
|listen|  |worker 1| |worker 2|   |     +----------+
+------+  +--------+ +--------+   |
                  |     +---------+     +---+
                  |               |     |LRU|
                  |               |     +---+
                  |               |
+-------+       +-v--+   +----+   |     +--------------+
|storage+------->IO 1|   |IO 2|   |     |slab allocator|
+-------+       +----+   +-^--+   |     +--------------+
                           |      |
+----------+               |      |     +--------------------+
|compaction+---------------+      |     |extstore page memory|
+----------+                      |     +--------------------+
                                  |
                                  |     +----------------------+
                                  +     |extstore write buffers|
                                        +----------------------+
```

##### THREADS

The storage thread is similar to the LRU maintainer (they may merge later). It examines the LRU tails for each slab class. It requests a write buffer from extstore, CRC32's the item data, then hands the write buffer back to extstore.

The compaction thread targets pages ready for compaction. It reads the page data back from storage, walks the items within, and either throws them away or rewrites into a new page. See below for more detail.

Multiple IO threads exist which issue pread/pwrite/etc calls to the underlying storage on behalf of worker, storage, and compaction threads. Each IO thread has a simple stack queue with a mutex.

There is also an "extstore maintenance thread", which simply sorts page data for other threads. It may disappear and become an API call for the LRU maintainer.

##### STRUCTURES

Extstore has some of its own data structures. Mostly stats counters, and page data.

Pages on disk are (default 64M) subsections of a file or device. Each page has a small structure in memory. The structure in memory is used to avoid writing to flash when objects are deleted from a page. A page can be reclaimed once fully empty, so a page can be recycled without reading or writing back to it.

Extstore has write buffers which items are written into from the storage or compaction threads. When write buffers are full, a write will fail temporarily while the buffer is send to an IO thread. Once flushed, writes are accepted into the buffer again. This is used to rate limit writes to storage.

How an item flows to storage:

```
+--------+
|LRU tail|
+--------+
  |
  | * Is the item large?               +------>
  |                                    |
  | * Is memory low?                   | Once written to buffer:
  |                                    |
  | * Is a write buffer available?     | * Allocate new small item
  |                                    |
  |   +------------+                   | * Copy key+metadata to new item
  +--->write buffer+-------------------+ * Also contains pointer to flash:
      +------------+                       [page, offset, version]

    * IF the write buffer was full:      * Replace original with small item

     +------------+     +---------+
     |write buffer+----->IO thread|
     +------------+     +---------+

    * All writes wait during flush
```

- If all memory is exhausted while writes to storage are waiting, items will be evicted from the LRU tail in memory. IE: An item in slab class 10 waiting to be written to storage, will instead be evicted. Sets to cache must not block on storage.
  
##### How an item is read from storage:

```
   +-------+
+--+request|
|  +-------+
|
|  +-----------------+
+-->hash table lookup|
   +-----------------+

    If item is a flash header:

    * Create IO request for extstore

    * Continue parsing other keys in request

    Before response is written to the client,
    if it contains IO objects:

 +--------------------------------------------+
1                     4
   +-------------+     +-------------+
+--+worker thread<-----+libevent poll<----+
|  +-------------+     +-------------+    |
|                                         |
|  +---------+       +------------------+ |
+-->IO thread+------->worker notify pipe+-+
   +---------+       +------------------+
2                   3
```

- With multigets, many IO objects can be sent to the IO thread at the same time. Typical flash devices are internally parallel, and can have similar latency for batched requests.
- Workers fill iovec's with response data while parsing each key in the request. For every item header, blank iovec's are added where the data would be.
- A temporary item is allocated from slab memory to hold the full item.
- If an item has become invalid (page evicted, etc), the IO thread will record a miss back into the IO object before shipping back to the worker.
- On a hit, the holes are filled in the iovec with the temporary item.
- On a miss, the affected iovec entries are are NULL'ed out, wiping the response.
- Temporary memory is freed at the end of the request.

Items read from storage have a chance of being recached into memory if they are read multiple times in less than a minute. This allows items which are only occasionally accessed to stay on storage. When they are recached into main memory, their flash entry is deleted.

##### The lifetime of a storage page:

```
[/data/extstore]

OFFSET + ID  + VERSION
       |     |
 0M    | P0  | 5
 64M   | P1  | 10
 128M  | P2  | 12
 192M  | P3  | 6
 256M  | P4  | 7
 320M  | P5  | 8
 384M  | P6  | 9
       |     |
       |     |
       |     |
       +     +
```

Storage pages are offsets into a file, with a default size of 64 megabytes per page. Future releases should allow multiple files (thus devices) be used for the same page pool. As of this writing, only buffered IO is used. O_DIRECT and async IO's are planned.

Each page has a version assigned to it as it is used. When a page is recycled or evicted, it is given a new version number.

Item headers in memory store: [PAGE ID, OFFSET IN PAGE, VERSION NUMBER]. The version number is used to validate an object is still valid when being fetched from storage.

Pages are organized into logical "buckets". In the extstore shim, buckets are arbitrary numbers. Memcached's higher levels give the buckets meaning

```
OPEN      [BUCKETS]     FULL

          +-------+
[P9]<-----+DEFAULT+---->[P0,P1,P2,P5,P6]
          +-------+

          +---------+
[P10]<----+COMPACTED+-->[P3,P4]
          +---------+

          +-------+
[P11]<----+LOW TTL+---->[P7,P8]
          +-------+


FREE: [P12,P13,...]
```

Buckets are used to coalesce data into related pages. By default all items are written into bucket 0 [DEFAULT].

- If an item has been rewritten during compaction (see below), it is written into [COMPACTED] which are reserved for items having survived compaction.
- If an item has a low remaining TTL at flush time (see ext_low_ttl option), it will be written into the [LOW TTL] bucket. This bucket is excluded from compaction. Pages are reclaimed periodically as the items expire.

[COMPACTED] pages tend to contain very long lived objects, so those pages have less fragmentation and need to be rewritten less often.

[LOW TTL] pages are never compacted, which avoids write amplification at the cost of some space over time.

In the future, [LOW TTL] may split into multiple buckets. Splitting IE: low, med, high, would allow very low pages to reclaim faster while still avoiding write amplification for higher TTL's.

##### How deletes, and overwrites work:

```
+--------+         +----+
|ITEM HDR+----+----+PAGE|
+--------+    |    +----+
              |
[DELETE]      | [1048576 bytes stored]
              | [6000 objects stored]
* Remove from |
  hash table  | ---
              |
              | [1048401 bytes stored]
              | [5999 objects stored]
              |
[REPLACE]     |
              | ---
* Insert into |
  hash table  | No change
              |
              +
```
              
Pages track a few counters in memory only. The number of bytes stored, and the number of objects stored. A page has no way of retrieving what items are actually stored within it.

Deleting an object from a page reduces the object and byte count from the in-memory counters associated to that page. The same goes when page are being filled in the first place, the counters are incremented.

- When bytes and objects in a page reach zero, it is immediately reaped and placed onto the free list.
- Deletes or overwrites never cause new writes to happen to storage.

##### The compaction thread:

- First, find a candidate to compact. IE: page is more than 50% empty. (tuned by `ext_max_frag`).
- Since pages are written to aligned by write buffer size, the thread reads data back from the page in chunks the size of the write buffer (IE: 8M).
- The buffer is walked linearly. Since items are fully written to storage, the read buffer can be parsed as item structures. Once an item is examined, the next one is found by advancing a pointer by item size.
- When an item is examined:
  - The key hash is retrieved from storage pre-calculated, which speeds up readback.
  - An item lock is taken, and the hash table is checked for the original item.
  - If the item exists, and is still an item header which points into the same page at the same version, it's queued to be rewritten.
  - IF `ext_drop_unread` is enabled, a valid item is only rescued if it was recently accessed (specifically; if it's not in the COLD LRU).

`drop_unread` is important if a lot of data is being written through storage. This ensures objects which are getting requested, but not frequently enough to be recached into memory, aren't lost.

##### Memory thresholds:

The slab page balancer has an algorithm specific for extstore configurations.

For reference, a python and internal C version of the algorithm exist.

When tuning changes to the algoritm, it's easy to disable the internal algorithm and use external scripts.

Since the extstore system is designed to send items to storage before they're removed from memory, it needs to manage a buffer of free space. 1% or 0.5%.

- In each slab class, 0.5% of chunks assigned to the class "should be free"
- Overall, 0.5% of (1M) memory pages should be kept in the global page pool, to be assigned as needed.

These targets allow the storage thread some time to react to bursts of traffic without having to drop memory. If free memory drops below the threshold, the storage thread works to catch up.

- Item headers use memory out of the global page pool, which ensures memory is available as items are pushed to storage.
- If the global pool gets low, pages are reclaimed from the larger slab classes.

NOTE: The algorithm is currently does not prevent small items from taking all memory. If items are relatively small, or a very large amount of storage space is available compared to RAM, there will be no memory to hold other items. This will cause the flash to be hit much harder as very recent items are written out.

Caps may be introduced in the future.

## Restartable Cache

Memcached 1.5.18 and newer can recover its cache between restarts. It can restart after upgrades of the binary, most changes in settings, and so on. It now also supports using persistent memory via DAX filesystem mounts. See below for more details.

Use it by adding: `-e /tmpfs_mount/memory_file` to your startup options.

`/tmpfs_mount/` must be a ram disk of some sort, big enough to satisfy the memory limit specified on startup with -m. To gracefully restart; send a SIGUSR1 signal to the daemon, and wait for it to shut down and exit.

It will create a `/tmpfs_mount/memory_file.meta` file on shutdown. On restart it will read this file and ensure the restart is compatible. If it is not compatible or the file is corrupt, it will start with a clean cache.

If you change some parameters the cache will come up clean:

- The memory limit (`-m`)
- The max item size.
- Slab chunk sizes.
- Whether CAS is enabled or not.
- Whether slab reassignment is allowed or not.

... you are able to change all other options inbetween restarts!

Important Caveats (see below for more detail):

- System clock must be set correctly and must not jump while memcached is down.
- Deletes, sets, adds, incr/decr/etc commands will be missed while instance restarts!
- The earliest version you can restart from is 1.5.18. Older versions cannot upgrade to 1.5.18 without losing the cache.

Consider this feature experimental for the next few releases, but please give it a try and let us know what you think.

### DETAILS

This works by putting memory related to item data into an external mmap file (specified via `-e`). All other memory: the hash table, connection memory, etc, stay in main RAM. When the daemon is restarted, it runs a pass over the item data and fixes up the internal pointers and regenerates the hash table. This typically takes a few seconds, but if you have close to a billion items in memory it can take two or three minutes.

Once restarted, there is no performance difference between restartable and non-restartable modes.

#### DAX mounts and persistent memory

If you have a persistent memory device, you can utilize this feature to extend memcached's memory into persistent memory. This does not make memcached crash safe! It will put item memory into your persistent memory mount, while the rest of memory (hash table/buffers/etc) use system DRAM. This is a very high performance mode as the majority of memory accesses stay in DRAM. Also, with a graceful shutdown memcached can be restarted after reboots so long as the DAX mount persists.

See your persistent memory vendor's documentation for how to configure a DAX mount.

You can see extensive testing we did in this and other modes on our blog: https://memcached.org/blog/persistent-memory/

#### CAVEATS

Your system clock must be set correctly, or at least it must not move before memcached has restarted. The only way to tell how much time has passed while the binary was dead, is to check the system clock. If the clock jumps forward or backwards it could impact items with a specific TTL.

Users must keep in mind that while an instance is stopped, it may be missing updates from the network; deletes, sets, and so on. It is only safe to restart memcached if your architecture can handle this by either pausing/buffering updates, or restarting at a time when no changes are happening to the cache.

#### FUTURE WORK

This feature is presently incompatible with extstore. This should be resolved in the next version or two. Further additions to usability, safety, etc, will be added based on feedback we receive.

## Memcached API clients

The reset button has been hit on the clients page. If you have a client you want to list, either you wrote or enjoy, please submit a pull request or patch to the wiki.

The old list was largely out of date. Many clients have come and gone. If you want to find out what to use you should search for information regarding the language you are using.

Most languages have module indexes (like CPAN, NPM, Gem, etc). With those and googling of tutorials and articles you can learn about the different clients available to you and pick one which best suits your needs.

## Commands

Memcached handles a small number of basic commands.

Full documentation can be found in the Protocol Documentation.

### Standard Protocol

The "standard protocol stuff" of memcached involves running a command against an "item". An item consists of:

- A key (arbitrary string up to 250 bytes in length. No space or newlines for ASCII mode)
- A 32bit "flag" value
- An expiration time, in seconds. '0' means never expire. Can be up to 30 days. After 30 days, is treated as a unix timestamp of an exact date.
- A 64bit "CAS" value, which is kept unique.
- Arbitrary data


CAS is optional (can be disabled entirely with -C, and there are more fields that internally make up an item, but these are what your client interacts with.

#### No Reply

Most ASCII commands allow a "noreply" version. One should not normally use this with the ASCII protocol, as it is impossible to align errors with requests. The intent is to avoid having to wait for a return packet after executing a mutation command (such as a set or add).

The binary protocol properly implements noreply (quiet) statements. If you have a client which supports or uses the binary protocol, odds are good you may take advantage of this.

#### Storage Commands

##### set

Most common command. Store this data, possibly overwriting any existing data. New items are at the top of the LRU.

##### add

Store this data, only if it does not already exist. New items are at the top of the LRU. If an item already exists and an add fails, it promotes the item to the front of the LRU anyway.

##### replace

Store this data, but only if the data already exists. Almost never used, and exists for protocol completeness (set, add, replace, etc)

##### append

Add this data after the last byte in an existing item. This does not allow you to extend past the item limit. Useful for managing lists.

##### prepend

Same as append, but adding new data before existing data.

##### cas

Check And Set (or Compare And Swap). An operation that stores data, but only if no one else has updated the data since you read it last. Useful for resolving race conditions on updating cache data.

#### Retrieval Commands

##### get

Command for retrieving data. Takes one or more keys and returns all found items.

##### gets

An alternative get command for using with CAS. Returns a CAS identifier (a unique 64bit number) with the item. Return this value with the cas command. If the item's CAS value has changed since you gets'ed it, it will not be stored.

#### delete

Removes an item from the cache, if it exists.

#### incr/decr

Increment and Decrement. If an item stored is the string representation of a 64bit integer, you may run incr or decr commands to modify that number. You may only incr by positive values, or decr by positive values. They does not accept negative values.

If a value does not already exist, incr/decr will fail.

#### Statistics

There're a handful of commands that return counters and settings of the memcached server. These can be inspected via a large array of tools or simply by telnet or netcat. These are further explained in the protocol docs.

##### stats

ye 'ole basic stats command.

##### stats items

Returns some information, broken down by slab, about items stored in memcached.

##### stats slabs

Returns more information, broken down by slab, about items stored in memcached. More centered to performance of a slab rather than counts of particular items.

##### stats sizes

A special command that shows you how items would be distributed if slabs were broken into 32byte buckets instead of your current number of slabs. Useful for determining how efficient your slab sizing is.

WARNING this is a development command. As of 1.4 it is still the only command which will lock your memcached instance for some time. If you have many millions of stored items, it can become unresponsive for several minutes. Run this at your own risk. It is roadmapped to either make this feature optional or at least speed it up.

##### flush_all

Invalidate all existing cache items. Optionally takes a parameter, which means to invalidate all items after N seconds have passed.

This command does not pause the server, as it returns immediately. It does not free up or flush memory at all, it just causes all items to expire.

## Memcached Text Protocol Meta Commands

NOTE: These commands are new. While at this point we don't expect to make incompatible changes, there is a small chance it could happen so please keep an eye on the release notes. If you use or have any issues with them, please let us know!

Memcached has additional commands which are used to reduce bytes on the wire, reduce network roundtrips required for complex queries (such as anti-dogpiling techniques), expose previously hidden item information, and add many new features. These are in addition to the existing Text Protocol, and can do everything the Binary Protocol could do before.

The full description of the Meta commands are available in the Text Protocol documentation:

- Text Protocol

This wiki serves as a companion to protocol.txt with use cases and examples of the new commands.

### Command Basics

Commands have a basic request/response headers which look like:

```
set request:
 ms foo S2 T90 F1\r\n
 hi\r\n
response:
 ST\r\n

get request:
 mg foo t f v\r\n

response:
 VA 2 s2 t78 f1\r\n
 hi\r\n

delete request:
 md foo I\r\n

response:
 DE\r\n
```

Commands are 2 characters, followed by key, flags, and tokens requested by flags. Responses are a 2 character code, any additional flags added (for mg), and any requested tokens. Flag responses are in the order set by the order in the client request.

For full detail please see Text Protocol documentation.

### Replacing GET/GETS/TOUCH/GAT/GATS

Standard GET:

`mg foo t f v`

GETS (get with CAS):

`mg foo t f c v`

TOUCH (just update TTL, no response data):

`mg foo T30`

... will update the TTL to be 30 seconds from now.

GAT (get and touch):

`mg foo t f v T90`

... will fetch standard data and update the TTL to be 90 seconds from now.

GATS (get and touch with CAS):

`mg foo t f c v T100`

... same as above, but also gets the CAS value for later use.

### New MetaData Flags
- `l` flag will show the number of seconds since the item was last accessed.
- `h` flag will return 0 or 1 based on if the item has ever been fetched since being stored.
- `t` flag will return the number of seconds remaining until the item expires (-1 for infinite)

Others will be documented in protocol.txt

### Atomic Stampeding Herd Handling
We can use the CAS value and some new flags to do stampeding herd (dogpiling) protection with a minimal number of round trips to the server:

```
request:
 mg foo f c v N30\r\n
response:
 VA 0 f0 c2 W\r\n
\r\n
```

The `N` flag instructs memcached to automatically create an item on miss, with a supplied TTL, which is 30 seconds in this example.

In the flags returned a new flag `W` has been added. This is instructing the client that it has received a miss and has "Won" the right to recache the item. The client may then directly update the value once it has been fetched or calculated, or set back using the CAS value it retrieved.

If a different client requests the same item in this time, it will see a slightly different response:

```
request:
 mg foo f c v N30\r\n
response:
 VA 0 f0 c2 Z\r\n
\r\n
```

The `Z` flag indicates to the client that a W flag has already been sent, and this is a special non-data item. The client may then retry, wait, or take a different approach.

This approach was done in the past by having clients race with an add command after a miss, using special response values, or so on. It is now collapsed into the normal request/response workflow, and has additional features.

### Early Recache

The 'R' flag can be used to do an anti-herd early recache of objects nearing their expiration time. This can be used to reduce misses for frequently accessed objects. Infrequently accessed objects can be left to expire.

```
request:
 mg foo v t R30\r\n
response:
 VA 2 t29 W\r\n
hi\r\n
```

In this example, the R flag is supplied a token of '30', meaning if the TTL remaining on an item is less than 30 seconds, attempt to refresh the item.

In the response we see the extra 'W' (win) flag has been returned, indicating to this client that it should recache the item. Any further clients will instead see a 'Z' flag, indicating a request has already been sent.

We also see the TTL remaining, via the 't' flag, confirming that the TTL is below 30 seconds.

The CAS value may also be requested via the 'c' flag, which allows honoring the recache only if the object hasn't changed since the win token was received.

```
get request:
 mg foo v c R30\r\n
response:
 VA 0 c999\r\n
\r\n

set request:
 ms foo S3 C999
 new\r\n
response:
 ST\r\n
```

### Serve Stale

Intentionally serving stale but usable data is possible with the meta commands, similar to Stale-While-Revalidate or Stale-While-Error in HTTP.

In some cases you would want to actively mark an in-memory item as stale. You can then either have the first client fetching the stale value also handle revalidation, or kick off an asynchronous recache but still inform clients that the data may not be up to date.

We use the meta delete command to mark an item as stale.

```
request:
 md foo I T30\r\n
response:
 DE\r\n
```

We also optionally change the TTL. In this instance stale data will be served for a maximum of 30 seconds. Marking an item as stale also changes its CAS ID number.

```
request:
 mg foo t c v
response:
 VA 4 t29 c777 W X\r\n
 data\r\n
```

The next metaget gets the 'W' flag, indicating it has rights to exclusively recache the item. It also gets the 'X' flag, indicating that the item is stale. How this is handled is up to the application; it can use the data as-is, adjust it, warn a user, or quietly re-fetch later.

If another metadelete comes in before the item above is recached, the CAS will change to 778 (or higher), and the later metaset call will fail.

However, it is possible to still update the item but keep it marked as stale, via the 'I' flag with metaset.

```
request:
 ms foo S3 T360 C777 I\r\n
 new\r\n
response:
 ST\r\n
```

The next metaget will continue to see the item as stale, with its previous TTL and previous CAS:

```
request:
 mg foo t c v\r\n
response:
 VA 3 t25 c778 X\r\n
 new\r\n
```

The CAS value must be lower than the real CAS value for this to apply. Once a set matching the CAS value comes in, all data is overwritten and the TTL is updated properly.

### Pipelining Quiet mode with Opaque or Key

Pipelining with meta commands is done with combinations of the 'q', 'O', and 'k' flags. These flags work for all of the metaget, metaset, and metadelete commands.

In the normal text protocol, the requested key is reflected back to the client. This makes it possible to differentiate responses when requesting many keys.

```
get bar foooooooooooooooooooooooooooooooo baz
VALUE foooooooooooooooooooooooooooooooo 0 2
hi
END
```

In the above, only foo+ exists. The rest of the values don't have lines indicating a miss, only the END token after all keys are fetched.

This is still true even when fetching a single key:

```
get foooooooooooooooooooooooooooooooo
VALUE foooooooooooooooooooooooooooooooo 0 2
hi
END
```

Metagets do not have a multi-key interface. Requesting many keys at once requires pipelining the commands:

```
mg foo v\r\nmg bar v\r\nmg baz v\r\n
```

Metaget will also not return the key by default. Clients should look for the response codes to count responses.

It's possible to optimize fetching many keys by using the 'q' flag, which will hide the "EN" code on miss. There is a problem with this: you can no longer differentiate the responses to match item data to requested keys.

There are two options for optimizing pipelined requests:

```
request:
 mg foo t c v q k\r\n
response:
 VA 2 s2 t-1 c2 kfoo\r\n
 hi\r\n
```

The 'k' flag will add the key as a token in the response. This works for `mg`, as well as `ms` and `md`.

This is still not ideal if your keys are very long, especially if the data stored is very small (perhaps even 0 bytes!). Returning 200 byte keys with 8 bytes of data is a lot of extra bytes on the wire.

There is one more option, the 'O' (opaque) flag. Tokens supplied with this flag are reflected back in the response as-is. Opaque tokens can be up to 32 bytes in length as of this writing. They can be alphanumeric but numbers are likely the common use case.

```
request:
 mg foo v q Oopaque\r\n
response:
 VA 2 Oopaque\r\n
 hi\r\n
```

In this example the string "opaque" is used as a token to make it stand out more in the response. This can be any (short) ascii string. A simple approach would be to simply count the number of outstanding requests, using numerics and resetting after receiving the responses. This keeps the numbers short, with some benefit from hex encoding if desired.

Finally, it's still impossible to tell when all of the keys have been processed if they were all misses. There are two options:

1. Send the final mg without the 'q' flag, which will add an EN response code.
2. Use the mn meta no-op command. All this command does is respond with a bare MN code.

```
request:
 mn\r\n
response:
 MN\r\n
```

Stick it at the end of a pipeline:

`mg [etc]\r\nmg [etc]\r\nmg [etc]\r\nmn\r\n`

### Quiet mode semantics

The 'q' flag is described briefly in the section about Pipelining, but what else does it do?

In general, the 'q' flag will hide "nominal" responses. If you wish to pipeline a bunch of sets together but don't want all of the "ST" code responses, pass the 'q' flag with it. If a set results in a code other than "ST" (ie; "EX" for a failed CAS), the response will still be returned.

Any syntax errors will still result in a response as well (CLIENT_ERROR).

### Probabilistic Hot Cache

A new technique made possible with the meta commands is a probabilistic client-side hot key cache. This means a coordination-free method of populating a local cache to avoid making requests to memcached for very frequently accessed items.

Using the new h and l flags, we can see if an item has been hit before, and how many seconds it's been since it was last hit. Lets combine these:

```
request:
 mg foo v h l\r\n
response:
 VA 4 h1 l5\r\n
 data\r\n
```

Breaking down the flags:

- `s` is data size, getting a 4 in the response.
- `v` means return the value, getting "data\r\n" in the response block.
- `h` means return a 0 or 1 depending on if the item has been requested since it was stored.
- `l` means the time in seconds since last access, which here shows 5 seconds.

We can weight these values to create a probabilistic cache: `if (h == 1 && l < 5 && random(1000) == 0) { add_to_local_cache(it); }`

The random factor here is a weight that could be other factors (database/network/system load/local cache hit rate/etc). Keys which suddenly get a lot of traffic will filter in slowly, with the highest traffic keys being cached very quickly.

This approach requires no live coordination between servers to discover or communicate "hot keys". Implementations of this method do need to make a decision on how validation is done.

#### Hot Key Cache Invalidation

For truly hot keys, the simplest approach is to only cache them in a client for a couple seconds. A "shadow key" with a slightly longer expiration time (30 seconds or so) could be left in its place to signal to a client to immediately recache a key if seen again, This covers a wide number of use cases. The total number of requests to memcached will be higher than in a perfect system, but the zero coordination effort and natural key discovery without server overhead is a huge bonus.

Another approach, especially useful for items which are large or are CPU intensive to deserialize, is to periodically revalidate items.

```
request:
 mg foo v h l c\r\n
response:
 VA 4 h1 l5 c500\r\n
 data\r\n
```

This time we also request the CAS value. A client could schedule asynchronous periodic revalidations for hot keys in the background. Metaget can fetch items without the value.

```
request:
 mg foo c\r\n
response:
 HD c500
```

(the `HD` status code indicates no value is being received; only a header)

In this case, we only care about finding if the CAS value is identical.

We may add support for conditionally fetching the value if CAS does or does not match, to further improve this use case. Check doc/protocol.txt with your tarball for the most up to date information.

## CommonFeatures

### Common Features

Clients have a set of common features that they share. The intent here is to give you an overview of how typical clients behave, and some helpful features to look for.

This section does not describe which clients implement what, but merely what most do implement.

#### Hashing

All clients should be able to hash keys across multiple servers.

#### Consistent Hashing

Most clients have the ability to use consistent hashing, either natively or via an external library.

#### Storing Binary Data or Strings

If passed a flat string or binary data, all clients should be able to store these via set/add/etc commands.

#### Serialization of Data Structures

Most clients are able to accept complex data structures when passed in via set/add/etc commands. They are serialized (usually via some form of native system), a special flag is set, and then the data is stored.

Clients are not able to store all types of complex structures. Objects are usually not serializable, such as row objects returned from a mysql query. You must turn the data into a pure array, or hash/table type structure before being able to store or retrieve it.

Since item flags are used when storing the item, the same client is able to know whether or not to deserialize a value before returning it on a 'get'. You don't have to do anything special.

#### Compression

Most clients are able to compress data being sent to or from a server. They set a special flag bit if data is over a certain size threshold, or it is specifically requested. Then compress the data and store it.

Since item flags are used, the clients will automatically know whether or not to decompress the value on return.

#### Timeouts

Various timeouts, including timeouts while waiting for a connection to establish, or timeouts while waiting for a response.

#### Mutations

Standard mutations as listed in Commands

#### Get

Standard fetch commands as listed in Commands

#### Multi-Get

Most clients implement a form of multi-get. Exactly how the multi-get is implemented will vary a bit.

Given a set of 10 keys you wish to fetch, if you have three servers, you may end up with 3-4 keys being fetched from each server.

- Keys are first sorted into which servers they map onto
- Gets are issued to each server with the list of keys for each. This attempts to be efficient.
- Depending on the client, it might write to all servers in parallel, or it might contact one at a time and wait for responses before moving on.

Find that your client does the latter? Complain to the author ;)

### Less Common Features

#### Get-By-Group-Key

In the above case of Multi-Get, sometimes having your keys spread out among all servers doesn't make quite as much sense.

For example, we're building a list of keys to fetch to display a user's profile page. Their name, age, bio paragraph, IM contact info, etc.

If you have 50 memcached servers, issuing a Multi-Get will end up writing each key to individual servers.

However, you may choose to store data by an intermediate "key". This group key is used by the client to discover which server to store or retrieve the data. Then any keys you supply are all sent to that same server. The group key is not retained on the server, or added to the existing key in any way.

So in a final case, we have 50 memcached servers. Keys pertaining to a single user's profile are stored under the group key, which is their userid. Issuing a Multi-Get for all of their data will end up sending a single command to a single server.

This isn't the best for everything. Sufficiently large requests may be more efficiently split among servers. If you're fetching fixed set of small values, it'll be more efficient on your network to send a few packets back and forth to a single server, instead of many small packets from many servers.

#### Noreply/Quiet

Depending on if noreply is implemented via ascii or not, it may be difficult to troubleshoot errors, so be careful.

Noreply is used when you wish to issue mutations to a server but not sit around waiting for the response. This can help cut roundtrip wait times to other servers. You're able to blindly set items, or delete items, in cases where you don't really need to know if the command succeeds or not.

#### Multi-Set

New in some binary protocol supporting clients. Multi-Set is an extension of the "quiet" mode noted above. With the binary protocol many commands may be packed together and issued in bulk. The server will respond only when the responses are interesting (such as failure conditions). This can end up saving a lot of time while updating many items.

## Programming

This basic tutorial shows via pseudocode how you can get started with integrating memcached into your application. If you're an application developer, it isn't something you just "turn on" and then your site goes faster. You have to pay attention.

If you're confused on how memcached works and integrates into an application, you may want to read the [TutorialCachingStory] if you haven't yet.

### Basic Data Caching

The "hello world" of memcached is to fetch "something" from somewhere, maybe process it a little, then shove it into the cache, to expire in N seconds.

#### Initializing a Memcached Client

Read the documentation carefully for your client.

```perl
my $memclient = Cache::Memcached->new({ servers => [ '10.0.0.10:11211', '10.0.0.11:11211' ]});
```

```
memcli = new Memcache
memcli:add_server('10.0.0.10:11211')
```

Some rare clients will allow you add the same servers over and over again, without harm. Most will require that you carefully construct your memcached client object once at the start of your request, and perhaps persist it between requests. Initializing multiple times may cause memory leaks in your application or stack up connections against memcached until you cause a failure.

#### Wrapping an SQL Query

Memcached is famous for reducing load on SQL databases. Unlike a query cache which can be centralized, implemented in slow middleware, or mass invalidated, you can easily get yourself moving by caching query results.

```perl
 # Don't load little bobby tables
sql = "SELECT * FROM user WHERE user_id = ?"
key = 'SQL:' . user_id . ':' . md5sum(sql)
 # We check if the value is 'defined', since '0' or 'FALSE' # can be
 # legitimate values!
if (defined result = memcli:get(key)) {
	return result
} else {
	handler = run_sql(sql, user_id)
	# Often what you get back when executing SQL is a special handler
	# object. You can't directly cache this. Stick to strings, arrays,
	# and hashes/dictionaries/tables
	rows_array = handler:turn_into_an_array
	# Cache it for five minutes
	memcli:set(key, rows_array, 5 * 60)
	return rows_array
}
```

Wow, zippy! When you cache this user's row(s), they will now see that same data for up to five minutes. Unless you actively invalidate the cache when a user makes a change, it can take up to five minutes for them to see a difference.

Often this is enough to help. If you have some complex queries, such as a count of users or number of posts in a thread. It might be acceptable to limit how often those queries can be issued by having a flat cache.

#### Wrapping Several Queries

The more processing that you can turn into a single memcached request, the better. Often you can replace several SQL queries with a single, fast, memcached lookup.

```perl
sql1 = "SELECT * FROM user WHERE user_id = ?"
sql2 = "SELECT * FROM user_preferences WHERE user_id = ?"
key  = 'SQL:' . user_id . ':' . md5sum(sql1 . sql2)
if (defined result = memcli:get(key)) {
	return result
} else {
	# Remember to add error handling, kids ;)
	handler = run_sql(sql1, user_id)
	t[info] = handler:turn_into_an_array
	handler = run_sql(sql2, user_id)
	t[pref] = handler:turn_into_an_array
	# Client will magically take this hash/table/dict/etc
	# and serialize it for us.
	memcli:set(key, t, 5 * 60)
	return t
}
```

When you load a user, you fetch the user itself and their site preferences (whether they want to be seen by other users, what theme to show, etc). What was once two queries and possibly many rows of data, is now a single cache item, cached for five minutes.

#### Wrapping Objects

Some languages allow you to configure objects to be serialized. Exactly how to do this in your language is beyond the scope of this document, however some tips remain.

- Consider if you actually need to serialize a whole object. Odds are your constructor could pull from cache.
- Serialize it as efficiently and simply as possible. Spending a lot of time in object setup/teardown can drag CPU.

Further consider, if you're deserializing a huge object for a request, and then using one small part of it, you might want to cache those parts separately.

#### Fragment Caching

Once upon a time ESI (Edge Side Includes) were all the rage. Sadly they require special proxies/caching/etc. You can do this within your app for dynamic, authenticated pages just fine.

Memcached isn't just all about preventing database queries. You can cache computed items or objects as well.

```perl
 # Lets generate a bio page!
user          = fetch_user_info(user_id)
bio_template  = fetch_biotheme_for(user_id)
page_template = fetch_page_theme
pagedata      = fetch_page_data

bio_fragment = apply_template(bio_template, user)
page         = apply_template(page_template, bio_fragment)
print "Content-Type: text/html", page
```

In this grossly oversimplified example, we're loading user data (which could be using a cache!), loading the raw template for the "bio" part of a webpage (which could be using a cache!). Then it loads the main template, which includes the header and footer.

Finally, it processes all that together into the main page and returns it. Applying templates can be costly. You can cache the assembled bio fragment, in case you're rendering a custom header for the viewing user. Or if it doesn't matter, cache the whole 'page' output.

```perl
key = 'FRAG-BIO:' . user_id 
if (result = memcli:get(key)) {
	return result
} else {
	user         = fetch_user_info(user_id)
	bio_template = fetch_biotheme_for(user_id)
	bio_fragment = apply_template(bio_template, user)
	memcli:set(key, bio_fragment, 5 * 15)
	return bio_fragment
}
```

See? Why do more work than you have to. The more you can roll up the faster pages will render, the happier your users.

### Extended Functions

Beyond 'set', there are add, incr, decr, etc. They are simple commands but require a little finesse.

#### Proper Use of `add`


add allows you to set a value if it doesn't already exist. You use this when initializing counters, setting locks, or otherwise setting data you don't want overwritten as easily. There can be some odd little gotchas and race conditions in handling of add however.

```perl
 # There can be only one
key = "the_highlander"
real_highlander = memcli:get(key)
if (! real_highlander) {
	# Hmm, nobody there.
	var = fetch_highlander
	if (! memcli:add(key, var, 3600)) {
		# Uh oh! Somebody beat us!
		# We can either use the variable we fetched,
		# or issue `get` again in case it might be newer.
		real_highlander = memcli:get(key)
	} else {
		# We win!
	    gloat
	}
}
return real_highlander
```

#### Proper Use of `incr` or `decr`

`incr` and `decr` commands can be used to maintain counters. Such as how many hits a page has received, when you rate limit a user, etc. These commands will allow you to add values from 1 or higher, or even negative values.

They do not, however, initialize a missing value.

```perl
# Got a hit!
key = 'hits: ' . user_id
if (! memcli:incr(key, 1)) {
	# Whoops, key doesn't already exist!
	# There's a chance someone else just noticed this too,
	# so we use `add` instead of `set`
	if (! memcli:add(key, 1, 60 * 60 * 24)) {
		# Failed! Someone else already put it back.
		# So lets try one more time to incr.
		memcli:incr(key, 1)
	} else {
		return success
	}
} else {
	return success
}
```

If you're not careful, you could miss counting that hit :) You can doll this up and retry a few times, or no times, depending on how important you think it is. Just don't run a set when you mean to do an add in this case.

### Cache Invalidation

Levelling up in memcached requires that you learn about actively invalidating (or revalidating) your cache.

When a user comes along and edits their user data, you should be attempting to keep the cache in sync some way, so the user has no idea they're being fed cached data.

#### Expiration

A good place to start is to tune your expiration times. Even if you're actively deleting or overwriting cached data, you'll still want to have the cache expire occasionally. In case your app has a bug, a crash, a network blip, or some other issue where the cache could become out of sync.

There isn't a "rule of thumb" when picking an expiration time. Sit back and think about your users, and what your data is. How long can you go without making your users angry? Be honest with yourself, as "THEY ALWAYS NEED FRESH DATA" isn't necessarily true.

Expiration times are specified in unsigned integer seconds. They can be set from 0, meaning "never expire", to 30 days (60*60*24*30). Any time higher than 30 days is interpreted as a unix timestamp date. If you want to expire an object on january 1st of next year, this is how you do that.

For binary protocol an expiration must be unsigned. If a negative expiration is given to the ASCII protocol, it is treated it as "expire immediately".

#### `delete`

The simplest method of invalidation is to simply delete it, and have your website re-cache the data next time it's fetched.

So user Bob updates his bio. You want Bob to see his latest info when he so vainly reloads the page. So you:

memcli:delete('FRAG-BIO: ' . user_id)
... and next time he loads the page, it will fetch from the database and repopulate the cache.

#### `set`

The most efficient idea is to actively update your cache as your data changes. When Bob updates his bio, take bob's bio object and shove it into the cache via 'set'. You can pass the new data into the same routine that normally checks for data, or however you want to structure it.

Play your cards right, and your database only ever handles writes, and data it hasn't seen in a long time.

#### Invalidating by Tag

TODO: link to namespacing document + say how this isn't possible.

### Key Usage

Thinking about your keys can save you a lot of time and memory. Memcached is a hash, but it also remembers the full key internally. The longer your keys are, the more bytes memcached has to hash to look up your value, and the more memory it wastes storing a full copy of your key.

On the other hand, it should be easy to figure out exactly where in your code a key came from. Otherwise many laborous hours of debugging wait for you.

#### Avoid User Input

It's very easy to compromise memcached if you use arbitrary user input for keys. The ASCII protocol uses spaces and newlines. Ensure that neither show up your keys, live long and prosper. Binary protocol does not have this issue.

#### Short Keys

64-bit UID's are clever ways to identify a user, but suck when printed out. 18446744073709551616. 20 characters! Using base64 encoding, or even just hexadecimal, you can cut that down by quite a bit.

With the binary protocol, it's possible to store anything, so you can directly pack 4 bytes into the key. This makes it impossible to read back via the ASCII protocol, and you should have tools available to simply determine what a key is.

#### Informative Keys

```sql
key = 'SQL' . md5sum("SELECT blah blah blah")
```

... might be clever, but if you're looking at this key via tcpdump, strace, etc. You won't have any clue where it's coming from.

In this particular example, you may put your SQL queries into an outside file with the md5sum next to them. Or, more simply, appending a unique query ID into the key.

```sql
key = 'SQL' . query_id . ':' . m5sum("SELECT blah blah blah")
```

## ProgrammingFAQ

### Basics

#### How can you list all keys?

With memcached, you can't list all keys. There is a debug interface, but that is not an advisable usage.

##### Why are you trying to dump the cache?

Think about why you need to do it. If you're trying to troubleshoot your application, we find that it's often more informative to look at the flow rather than the current state. Run memcached in a screen session with `-vv` or `-vvv` to have it print what it's doing. You may also use MaatKit to use tcpdump to analyze traffic to an instance.

Watching the flow is very useful. You can see if your application is fetching keys inappropriately (one at a time vs multiget, or repeatedly in a single request), or you can see why memcached decided to invalidate a key (if ran in -vvv mode). Inspecting the state can't tell you any of this useful information anyway.

##### My application requires it

If it's a requirement that your application is able to pull or walk keys, you really want a database. Tokyo Tyrant, MySQL, etc, are good candidates for this. Memcached as a caching service cannot support the ability to safely walk keys without locking out all other operations. Adding indexes, multiversioning, etc, can make this possible but will lower memory and cpu efficiency.

You "can" via the debug interface `stats cachedump`, but that will only ever be a partial dump, and is slow.

#### Why only RAM?

Everything memcached does is an attempt to guarantee latency and speed. If you have to sometimes hit disk, that's no longer true.

#### Why no complex operations?

All operations should run in O(1) time. They must be atomic. This doesn't necessarily mean complex operations can never happen, but it means we have to think very carefully about them first. Many complex operations can be emulated on top of more basic functionality.

#### Why is memcached not recommended for sessions? Everyone does it!

If a session disappears, often the user is logged out. If a portion of a cache disappears, either due to a hardware crash or a simple software upgrade, it should not cause your users noticable pain. This overly wordy post explains alternatives. Memcached can often be used to reduce IO requirements to very very little, which means you may continue to use your existing relational database for the things it's good at.

Like keeping your users from being knocked off your site.

#### What about the MySQL query cache?

The MySQL query cache can be a useful start for small sites. Unfortunately it uses many global locks on the mysql database, so enabling it can throttle you down. It also caches queries per table, and has to expire the entire cache related to a table when it changes, at all. If your site is fairly static this can work out fine, but when your tables start changing with any frequency this immediately falls over.

Memory is also limited, as it requires using a chunk of what's directly on your database.

#### Is memcached atomic?

Aside from any bugs you may come across, yes all commands are internally atomic. Issuing multiple sets at the same time has no ill effect, aside from the last one in being the one that sticks.

#### Why is there a binary protocol?

Because it's awesome. TODO: link to the new protocol page.

#### How do I troubleshoot client timeouts?

See [Timeouts] for help.

### Setup Questions

#### How do I authenticate?

You don't! Well, you used to not be able to. Now you can. If your client supports it, you may use SASL authentication to connect to memcached.

Keep in mind that you should do this only if you really need to. On a closed internal network this ends up just being added latency for new connections (if minor).

#### How do you handle failover?

You don't. Some clients have a "failover" option that will try the next server in the case of a failure. As noted in Configuring Clients this isn't always the best idea.

#### How do you handle replication?

It doesn't. Adding replication to the system halves your effective cache size. If you can't handle even a few percent extra cache misses, you have serious problems. Even with replication, things can break. More moving parts. Software to crash.

#### Can you persist cache between restarts?

Yes, in some situations. See the documentation on warm restart.

#### Do clients and servers all need to talk to each other?

Nope. The less chatter, the more scalable.

### Monitoring

#### Why Isn't curr_items Decreasing When Items Expire?
Expiration in memcached is lazy. In general, an item cannot be known to be expired until something looks at it.

Think of it this way: You can add billions of items to memcached that all expire at the exact same second, but no additional work is performed during that second by memcached itself. Only as you attempt to retrieve (or update) those items will memcached ever notice that they shouldn't be there. At this point, curr_items will be decremented by each item it has seen expired.

We may also notice expired items while searching for memory for new items, though this isn't likely to create an observable difference in curr_items because we'll be replacing it with a new item anyway.

### Use Cases

#### When would you not want to use memcached?

It doesn't always make sense to add memcached to your application.

TODO: link to that whynot page here or just inline new stuff?

#### Why can't I use it as a database?

Because it's a cache. Storage engines will start to support this use case, but primarily there're benefits for treating this as a cache, even if you were using a Key/Value database, it can be useful to have a cache in front of it.

#### Can using memcached make my application slower?

Yes, absolutely. If your DB queries are all fast, your website is fast, adding memcached might not make it faster.

Also, this:

```perl
my @post_ids = fetch_all_posts($thread_id);
my @post_entries = ();
for my $post_id (@post_ids) {
	push(@post_entries, $memc->get($post_id));
}
# Yay I have all my post entries!
```

See that? Don't do that. Use a multi-get. Fetching a single item from memcached still requires a network roundtrip and a little processing. The more you can fetch at once the better.

### Architectural

#### Why can't we use memcached as a queue server?

TODO: need to expand on this more.

To be succinct: queue servers should either push to their workers, or notify their workers. If using memcached, they must constantly poll. Scaling out beyond one server also ends up being silly. Workers poll all servers, and that doesn't mesh well with memcached clients, who want to resolve keys to a particular server.

If you think your memcached client is broken because you're trying to use it with multiple memcached queues, it's not broken. Sorry :)

## ProgrammingTricks

### Namespacing

Memcached does not natively support namespaces or tags. It's difficult to support this natively as you cannot atomically expire the namespaces across all of your servers without adding quite a bit of complication.

However you can emulate them easily.

#### Simulating Namespaces with Key Prefixes

Using a coordinated key prefix, you can create a virtual namespace that spans your entire memcached cluster. The prefix can be stored in your configuration and changed manually, or stored in an external key.

#### Deleting By Namespace

Given a user and all his related keys, you want a one-stop switch to invalidate all of their cache entries at the same time.

Using namespacing, you would set up a tertiary key with a version number inside it. You end up doing an extra round trip to memcached to figure the namespace.

```perl
user_prefix = memcli:get('user_namespace:' . user_id)
bio_data    = memcli:get(user_prefix . user_id . 'bio')
```

Invalidating the namespace simply requires editing that key. Your application will no longer request the old keys, and they will eventually fall off the end of the LRU and be reclaimed.

Careful in how you implement the prefix. You'll want to use `add` so you don't blow away an existing namespace. You'll also want to initialize it to something with a low probability of coming up again.

An easy recommendation is a unix timestamp.

```perl
# Namespace management, basic fetch.
key = 'namespace:' . user_id
namespace = memcli:get(key)
if (!namespace) {
    namespace = time()
	if (! memcli:add(key, namespace)) {
		# lost the race.
		namespace = memcli:get(key)
		# Could re-test it and jump to the start of the loop, hard fail, etc.
	}
	# Send back the new namespace.
	return namespace
}
```

And on invalidation:

```perl
key = 'namespace:' . user_id
if (! memcli:incr(key, 1)) {
	# Increment failed! Key must not exist.
	memcli:add(key, time())
}
```

This isn't a perfect algorithm either, but a simple one. The key is initialized via a timestamp, and then incremented by one each time the data is to be invalidated. This works well if the invalidations are infrequent, as a missing key will always end up being replaced with a larger number than was slowly incremented from before.

You can drop the race condition further by using millisecond resolution instead of seconds, but that makes your key prefix longer. For bonus points, base64 encode the number before sticking it in front of the other keys.

#### Storing sets or lists

Storing lists of data into memcached can mean either storing a single item with a serialized array, or trying to manipulate a huge "collection" of data by adding, removing items without operating on the whole set. Both should be possible.

One thing to keep in mind is memcached's 1 megabyte limit on item size, so storing the whole collection (ids, data) into memcached might not be the best idea.

Steven Grimm explains a better approach on the mailing list: http://lists.danga.com/pipermail/memcached/2007-July/004578.html

Chris Hondl and Paul Stacey detail alternative approaches to the same ideal: http://lists.danga.com/pipermail/memcached/2007-July/004581.html

A combination of both would make for very scalable lists. IDs between a range are stored in separate keys, and data is strewn about using individual keys.

#### Managing lists with `append/prepend`

Assuming you pick a route of storing a list of ids (numbers) in one a memcached key, and fetch the full data of a sets items separately, you can use `append`/`prepend` for atomic updates.

Lets take an AJAX-y feature of managing a users' list of interests. You give the user a box to type into. They type "hammers" and your fancy AJAX script updates their list of interests with "hammers", and anyone viewing their profile can instantly see "hammers" added to their list of interests. Normally you either have to try a CAS update to fetch the old cache item, add the hammer interest-id, then re-set the value, or simply delete the cache and pull the whole list from the database on the next view.

Instead, you can use append. When adding an interest-id onto the users' interest cache entry, simply issue an append command with a binary packed string representing the id. Do this similar to incr/decr, as append will fail if the cache list doesn't already exist.

So `append`, if fail, load from database and run `add`, if fail, decide how much you care to ensure the item got in and do more work.

Now lets say that user adds several hundred interests, then goes back to the beginning and decides he doesn't like "hammers" anymore, as he actually likes nails more. How do you handle this? You still have the old options of trying to pull the list, edit it, and CAS it back in, or deleting the whole thing. Or you can maintain a blacklist.

When initializing the list, the first byte in the list can be a "zero marker", a whole four or eight byte (depending on how big your interest-ids are) value that contains nothing but zeros.

When you're loading the list in from memcached, the first set of items you read will be "blacklist" items. Once you hit a value of "0", start reading the list as items that are supposed to exist. You can check each item against the "blacklist" and not enter it into the list for display.

So for common cases where the list won't get too large, you can add stuff to the end and then prepend removed items to the front. If the cache gets blown and reloaded, it won't have the blacklisted items in it to begin with, so the cache entry is cleaned.

This has obvious limitations based on cache size, but is a clever way to handle avoiding excessively expensive recaching operations with fickle users.

#### Zero byte values

Don't do this:

```perl
if (data = memcli:get('helloworld')) {
	# Yay stuff!
}
```

... because you could have perfectly valid data that has a result of 0, or false, or empty. Empty keys are useful for advisory locks, caching status flags, and the like. If you can get all that you need from the existence of the key alone, you don't need to waste bytes with extra data.

#### Reducing key size

The smaller your keys, the less memory overhead you have. With smaller items in the lower slab classes this can matter even more, as shaving a few bytes could end up putting the item into a more efficient slab class. Also, keys are limited to 250 characters (effectively. this may be raised to 65k in the future).

Compress keys when it's easy or makes sense. "super_long_function_names_abstract_key" might be descriptive but is a waste. Boil it down to a function id you can grep your code for. "slfnak", or whatever.

Base64 encode long numbers. Easy enough to use a commandline program to turn that back into a numeric.

Binary protocol allows setting arbitrary keys. Instead of base64 encoding you can byte pack them down to their native size. Also instead of 'sflnak', you could pack a two byte identifier and map the number back to your code.

However, don't do any of this unless you're really hurting for extra memory. Some easy changes like this can sometimes save between 5 and 20% of your memory, but often buying a few gigs of ram is cheaper than your time.

#### Accelerating counters safely

TODO: This is a memcached/mysql hybrid for avoiding running `count(*) from table where user_id = ?` constantly. I'll be fleshing this out later since I see some bugs in what I have here (deadlocks?)

#### Rate limiting

TODO: There were a couple decent posts on this. I've seen some slides that were buggy. Need to round them up.

#### Ghetto central locking

While we don't recommend doing this for any "serious" locking situation, sometimes you would benefit from an advisory, sometimes reliable "lock" obtainable via memcached.

Given a cache item that is popular and difficult to recreate, you could end up with dozens (or hundreds) of processes slamming your database at the same time in an attempt to refill a cache. Discussed more below as the "stampeding herd" problem, we'll describe a simple method of using add to create an advisory "ghetto lock"

```perl
key  = "expensive_frontpage_item"
item = memcli:get(key)
if (! defined item) {
	# Oh crap, we have to recache it!
	# Give us 60 seconds to recache the item.
	if (memcli:add(key . "_lock", 60)) {
		item = fetch_expensive_thing_from_database
		memcli:add(key, item, 86400)
		memcli:delete(key . "_lock")
	} else {
		# Lost the race. We can do any number of things:
        # - short sleep, then re-fetch.
        # - try the above a few times, then slow-fetch and return the item
        # - show the user a page without this expensive content
        # - show some less expensive content
        # - throw an error
	}
}
return item
```

Worst case you can end up operating without the lock at all. Best case you can reduce the amount of parallel queries going on without adding more infrastructure.

Use at your own risk! 'add' can fail because the key already exists, or because the remote server was down. If your client doesn't give you a way to tell the difference, you have to make a decision on how hard to try before running the query anyway or throwing an error.

#### Avoiding stampeding herd

It's a big problem when cache misses on hot (or expensive) items cause a mess of application processes to slam your database for answers. There are a large array of choices one has to avoid this problem, and we'll discuss a few below.

##### Ghetto lock

As shown above, in a pinch you can reduce the odds of needing to run the query by using memcached's add feature.

##### Outside mutex

A third party centralized mutex can also be used. MySQL has `SELECT GET_LOCK() ... RELEASE_LOCK()` which is fast but requires bothering your database a little bit. Other services exist as well, but this author isn't confident enough in what he knows to recommend any ;)

##### Scaling expiration

A common trick is to use soft expiration values embedded in your cached object. If your object is due to expire in an hour, set it to actually expire in 1.5 hours or more. Inside your object set a "soft timeout" for when you think the object is old.

When you fetch an object and it has passed the soft timeout, you can pick any method that agrees with you to re-cache it:

- Do a "lock" as noted above. If you fail to aquire the lock, return the old cached item. Lock winner recaches.
- Also store a "hard" timeout, or just assume the hard timeout is soft timeout + a value. Randomly decide if you want to recache, and increase the odds of recaching the value the older the item is.
- Dispatch an asyncronous job to recache the object.
- etc.
  
The cache object can still go away for many reasons (server restart, LRU, eviction, etc). Use this as mitigation but not your only line of defense.

##### Gearmand or similar

Using a job server can be an easy win. Gearman is a common, fast, scalable job service. While funneling recache requests through a job server will certainly add overhead, you can selectively use the service or rely purely on background jobs. Perhaps you'll want to funnel high traffic users through gearmand, but no one else.

Gearmand has two magic tricks; asyncronous or syncronous job processing.

In the case of a scaling expiration value, you can issue an asyncronous job to recache the object, then return the cache to a user. Gearmand can collapse similar jobs down so you don't end up executing millions of them.

In the case of a syncronous update, gearmand can coalesce incoming jobs with the same parameters. So the first process to issue the job request will get a worker to recache the data. Every other procecss after him will "subscribe" to the results of that first job, and not create more parallelism. When the first job finishes, gearmand broadcasts the response to all listeners and they all continue forward as though they had issued the request directly.

Very handy.

#### Ghetto replication

Some clients may natively support replication. It will pick two unique servers to store a value on, and either linearly or randomly retrieve the value again. Sometimes you don't have this feature!

You can "try" but not guarantee replication by modifying your key and storing a value twice. If you want to store a highly retrieved value from three locations, you could add '1', '2', or '3' to the end of the key, and store them all. Then on fetch randomly pick one.

Has a lot of gotchas; storage failure means you're more likely to get stale data back. Cute hack if you're in a pinch though.

#### "Touching" keys with add

Create an item that you want to expire in a week? Don't always fetch the item but want it to remain near the top of the LRU for some reason? `add` will actually bump a value to the front of memcached's LRU if it already exists. If the `add` call succeeds, it means it's time to recache the value anyway.

## UserInternals

It is important that developers using memcached understand a little bit about how it works internally. While it can be a waste to overfocus on the bits and bytes, as your experience grows understanding the underlying bits become invaluable.

Understanding memory allocation and evictions, and this particular type of LRU is most of what you need to know.

### How Memory Gets Allocated For Items

Memory assigned via the -m commandline argument to memcached is reserved for item data storage. The primary storage is broken up (by default) into 1 megabyte pages. Each page is then assigned into slab classes as necessary, then cut into chunks of a specific size for that slab class.

Once a page is assigned to a class, it is never moved. If your access patterns end up putting 80% of your pages in class 3, there will be less memory available for class 4. The best way to think about this is that memcached is actually many smaller individaul caches. Each class has its own set of statistical counters, and its own LRU.

Classes, sizes, and chunks are shown best by starting up memcached with -vv:

```shell
$ ./memcached -vv
slab class   1: chunk size        80 perslab   13107
slab class   2: chunk size       104 perslab   10082
slab class   3: chunk size       136 perslab    7710
slab class   4: chunk size       176 perslab    5957
slab class   5: chunk size       224 perslab    4681
slab class   6: chunk size       280 perslab    3744
slab class   7: chunk size       352 perslab    2978
slab class   8: chunk size       440 perslab    2383
slab class   9: chunk size       552 perslab    1899
slab class  10: chunk size       696 perslab    1506
[...etc...]
```

In slab class 1, each chunk is 80 bytes, and each page can then contain 13,107 chunks (or items). This continues all the way up to 1 megabyte.

When storing items, they are pushed into the slab class of the nearest fit. If your key + misc data + value is 50 bytes total, it will go into class 1, with an overhead loss of 30 bytes. If your data is 90 bytes total, it will go into class2, with an overhead of 14 bytes.

You can adjust the slab classes with -f and inspect them in various ways, but those're more advanced topics for when you need them. It's best to be aware of the basics because they can bite you.

### What Other Memory Is Used

Memcached uses chunks of memory for other functions as well. There is overhead in the hash table it uses to look up your items through. Each connection uses a few small buffers as well. This shouldn't add up to more than a few % extra memory over your specified -m limit, but keep in mind that it's there.

### When Memory Is Reclaimed

In versions prior to 1.5.0, by default expired items are not actively reclaimed. If running old versions with -o modern start option, or post 1.5.0, a crawler periodically scans the cache and frees expired objects.

Also, if you fetch an expired item, memcached will find the item, notice that it's expired, and free its memory. This gives you the common case of normal cache churn reusing its own memory.

Items can also be evicted to make way for new items that need to be stored, or expired items are discovered and their memory reused.

### How Much Memory Will an Item Use

An item will use space for the full length of its key, the internal datastructure for an item, and the length of the data.

You can discover how large an Item is by compiling memcached on your system, then running the "./sizes" utility which is built. On a 32bit system this may look like 32 bytes for items without CAS (server started with -C), and 40 bytes for items with CAS. 64bit systems will be a bit higher due to needing larger pointers. However you gain a lot more flexibility with the ability to put tons of ram into a 64bit box :)

```shell
$ ./sizes 
Slab Stats	56
Thread stats	176
Global stats	108
Settings	88
Item (no cas)	32
Item (cas)	40
Libevent thread	96
Connection	320
----------------------------------------
libevent thread cumulative	11472
Thread stats cumulative		11376
```

### When Are Items Evicted

Items are evicted if they have not expired (an expiration time of 0 or some time in the future), the slab class is completely out of free chunks, and there are no free pages to assign to a slab class.

#### How the LRU Decides What to Evict

Memory is also reclaimed when it's time to store a new item. If there are no free chunks, and no free pages in the appropriate slab class, memcached will look at the end of the LRU for an item to "reclaim". It will search the last few items in the tail for one which has already been expired, and is thus free for reuse. If it cannot find an expired item however, it will "evict" one which has not yet expired. This is then noted in several statistical counters.

See LRU Documentation for detail on the latest LRU algorithm.

#### libevent + Socket Scalability

Memcached uses libevent for scalable sockets, allowing it to easily handle tens of thousands of connections. Each worker thread on memcached runs its own event loop and handles its own clients. They share the cache via some centralized locks, and spread out protocol processing.

This scales very well. Some issues may be seen with extremely high loads (200,00+ operations per second), but if you hit any limits please let us know, as they're usually solvable :)

## ServerMaint

### Watching Server Health

Memcached has a lot of statistical counters. Most are tallies, but others serve as warnings for an administrator to notice. Note that there are many tools and top programs floating around which serve to help you digest this information.

#### Issuing Commands

Monitoring scripts should ideally issue test sets/gets/deletes to the servers occasionally, timing the response of each. If it's slow to connect you might have a connection or network problem. If it's slow to respond the server might be swapping or in ill health. You should always strictly monitor the health of a server, but an extra layer can be appreciated.

#### Important Stats

These are found when issuing a simple `stats` command to a memcached server.

##### curr_connections

Lists the number of clients presently connected. Monitor that this number doesn't come too close to your max connection setting (`-c`).

##### listen_disabled_num

An obscure named statistic counting the number of times memcached has hit its connection limit. When memcached hits the max connections setting, it disables its listener and new connections will wait in a queue. When someone disconnects, memcached wakes up the listener and starts accepting again.

Each time this counter goes up, you're entering a situation where new connections will lag. Make sure it stays at or close to zero.

##### accepting_conns

Related to the above listen_disabled_num, if you're already connected to memcached you can see if it's hit max connections or not by checking if this value is 1 or 0.

##### limit_maxbytes

Sometimes it's nice to ensure that how you think memcached starts and how it actually starts are in line. By checking limit_maxbytes you can verify that the -m argument took. Occasionally people using init scripts can be misconfigured and memcached will start with default values.

##### cmd_flush

While not exactly server health per-se, this is a good one to generically monitor. Every time someone issues a flush_all command, all items inside the cache are invalidated, and this counter is incremented. Sometimes debug code or misinformed people can leave scripts or callbacks running to "invalidate" the entire cache. Watch this value in production and sound the alarms if it starts moving, unless you really intended it to.

#### `stats sizes`

With new enough versions of memcached, you can run `stats sizes` enable (or disable, or use the start option), and memcached will dynamically track how much space items take in relatively fine grained buckets.

Running `stats sizes` will show how well your items align with slab classes.

WARNING: If your version of memcached is old (prior to 1.4.27) this command can hang the cache while it scans all items. Can be very dangerous!

#### Slab imbalance

A more complicated issue is an imbalance of the amount of memory assigned per slab, compared to where your actual data wants to go. Confusing speech for a simple algorithm:

- Monitor the global 'evictions' stat. If it starts going up. (or you can skip straight to #2)
- Monitor the `evicted` and `evicted_nonzero` stats inside `stats items`. evicte`d_nonzero means the object was evicted early and did not have an infinite expiration time. This information is per-slab.
- Read the `total_pages` stats from `stats slabs`, and overlay those numbers with the evicted stats from above.
- If the slabs with the most evictions line up with the slabs containing the highest number of pages, you might legitimately be out of memory.
  - Alternatively (and perhaps more important) you can correlate the per-slab hitrate with the number of pages.
- If the slabs with the highest evictions do not line up with the pages very well, you need to restart memcached so it can re-allocate memory.
Recent versions of memcached (1.4.25 and later) have well tuned automated features for repairing this situation without having to restart memcached. See the release notes and https://github.com/memcached/memcached/blob/master/doc/protocol.txt for more information.

#### Troubleshooting Client Timeouts

See Timeouts for help.

### Stats for Application Health

Monitoring these stats can tell you how the available memory in memcached, and how your app behaves, changes efficiency.

#### Global hitrate

Hitrate is defined as: `get_hits / (get_hits + get_misses)`. The higher this value is, the more often your application is finding cache results instead of finding dead air. Watch that the value is both higher than what you expect, and watch to see if it changes with time or releases of your application.

#### Hitrate per slab

While not possible to calculate the actual hitrate per-slab, since a "get miss" doesn't know how large the item could have been, you can monitor the number of get_hits and cmd_set on a per-slab basis via `stats slabs`. Watch that the number of get_hits is usually above cmd_set (more fetches than updates is often what you want), and watch for changes over time.

#### Evictions

An item is "evicted" from the cache if it still has time to live, but ended up at the tail end of the LRU cache when it comes time to allocate a new item. While memcached tries a little to find an expired item from the tail, it doesn't guarantee it.

`stats items` shows per-slab eviction status. 'evicted' is the total number of items tossed early from that slab, 'evicted_nonzero' are the number of tossed items that did not have an unlimited expiration time, and 'evicted - evicted_nonzero' are the number of items tossed which were stored forever.

'evicted_time' notes how many seconds it's been since the last item to be evicted, was last fetched. If this number is small, it means you're evicting items which were recently used.

#### Looks Can be Deceiving

You see a low hit rate, a high eviction rate, and assume that you need to buy more memory for memcached. Unfortunately this isn't necessarily true either. Consider some potential scenarios:

- Your application sets large numbers of keys, most of which are never used again.
- Your application tests for one or more keys on most requests that do not exist and are not recached, such a flags or values for a buggy user.

The former can cause high eviction count even though the data was not important to begin with. Audit what our application stores and see if this is a fault.

The latter can show poor hitrates, as many get_misses are accounted for by this flag. A workaround for this is to have the app "recache" a flag with a value of "not set". So the flag stays around in cache even if it's disabled, and doesn't throw off your hitrate calculations.

### OS Health (avoid swap!)

Memcached interacts hard with the network and with RAM. It's common for people to monitor swap usage, which can cause severe performance degredation to memcached.

It's also important to watch your network stack. Linux, for example, sports a number of counters for its network interfaces related to dropped packets, underruns, crc errors, etc. Most switches also have per-port counters on packet issues or link issues. Degraded performance could trace down to bad wiring, bad switchport, a dying NIC, or a network driver in need of tuning.

### Upgrading
Most minor releases of memcached focus on fixing bugs and adding instrumentation. New features are rare and tend to only happen on middle version bumps now. IE; 1.2 to 1.4 added the binary protocol and a large number of counters. 1.4.0 to 1.4.1 was mostly bugfixes and missing counters.

Minor features may show up as well. SASL authentication support happened in the mid-1.4 series, but the change is isolated and should not break existing clients.

You should follow new releases and the release notes. It's often better to uprade early so you have the extra instrumentation when you need it, instead of wishing you had it when things go wrong.

## ClusterMaint

### Capacity Planning

Setting up graphs (See [Tools] and similar) for longterm monitoring of many memcached values is an important part of capacity planning. Watch trends over time and draw lines to decide when it's time to invest in seeing what your aplication is up to or adding more memory to the cluster.

### Upgrades

We're very careful about the high quality of memcached releases, but you should exercise proper caution when upgrading. Run a new release in any QA or dev environment you may have for a while, then upgrade a single server in production. If things look okay, slowly roll it out to the rest.

Running clusters with mixed versions can be a headache for administrators when using monitoring.

### Finding Outliers

If you're carefully graphing all servers, or using tools to monitor all servers, watch for outliers. Bugs in clients, or small numbers of hot keys can cause some servers to get much more traffic than others. Identify these before they become a hazard.

## Performance

We expect memcached to be fast. Occasionally folks test memcached and see something they don't expect. This is a short list of how we (the developers and users) expect memcached to perform. If you're seeing different, it's likely a configuration problem at some layer. You should troubleshoot or request help in getting a properly oiled setup.

### General Performance

Handles Extremely High Load
On a fast machine with very high speed networking, memcached can easily handle 200,000+ requests per second. With heavy tuning or even faster hardware it can go many times that. Hitting it a few hundred times per second, even on a slow machine, usually isn't cause for concern.

#### It Should Not Hang

Memcached operations are almost all O(1). Connecting to it and issuing a get or stat command should never lag. If connecting lags, you may be hitting the max connections limit. See ServerMaint for details on stats to monitor.

If issuing commands lags, you can have a number of tuning problems. Most common are hardware problems, not enough RAM (swapping), network problems (bandwidth, dropped packets, half-duplex connections). On rare occasion OS bugs or memcached bugs can contribute.

##### OS Issues / Firewalls

It's not usually necessary to run a stateful firewalling system in front of a memcached server. You might if you run them by default, or need to protect memcached from the internet at large.

These systems have limits in the number of connections that can pass through it in a certain number of time, and how fast those connections or packets can pass through. A lot of connectivity bugs end up being tracked back to someone's firewall.

#### It Should Respond Quickly

On a good day memcached can serve requests in less than a millisecond. After accounting for outliers due to OS jitter, CPU scheduling, or network jitter, very few commands should take more than a millisecond or two to complete.

#### Expiration Times Should Be Accurate

Memcached tries to expire items when you tell it to. To avoid hitting the clock constantly, it updates an internal clock value every second. This means your expiration times should be accurate up to the nearest second. So an expiration can be a second early or a second late, but should not waver beyond that.

Since expiration values are stored as timestamps, be sure your OS clock is correct. If your OS clock is set to some time in the future, you're storing items into memcached, and you suddenly adjust the clock far into the past, those items will not expire on time. Inversely, jumping a clock forward can prematurely expire items.

#### How It Handles set Failures

There are conditions which memcached will "fail" a set. If it cannot find free memory, if the object is too large, or some unknown factor. In most cases where you are issuing a set against an item that already exists, a failure will cause the old item to be removed from the cache.

Memcached assumes that if you are issuing a set, even if the item is invalid, that means your intent was to update the cache, and whatever it contains is now stale. It error's on the side of caution and throws the old item out for you.

### Theoretical Limits

#### Max Clients

Since memcached uses an event based architecture, a high number of clients will not generally slow it down. Users have hundreds of thousands of connected clients, working just fine.

There are a few hard limits:

- Each connected client uses some TCP memory. You can only connect as many clients as you have spare RAM
- There is only one thread to accept new client connections. If you are cycling connections very quickly, you can overwhelm the thread. Use persistent connections or UDP in this case.
- High connection churn requires OS tuning. You will run out of local ports, TIME_WAIT buckets, and similar. Do research on how to properly tune the TCP stack for your OS.

#### Maximum number of nodes in a cluster

##### From the Client Perspective

A large number of servers can slow down a client from a few angles. If you are calculating the server hash table on every request, that will slow down as you add servers. Most clients will calculate the table once on startup and reuse it between requests.

Looking up a server to issue a request is simply a hash lookup, so it is not a performance issue.

- Establishing too many TCP sockets from the client wastes RAM
- Disabling persistent connections means the client will likely connect to all servers on every request. The 3 way handshake will add latency and packets to each request.

##### The Multiget Hole

There was once a discussion on a facebook note noting that as you add servers to a cluster, the more spread out multigets are. If you issue a multiget request for 10 keys against a 2 server cluster, that will turn into two syscalls; one for each server. If you have 10 servers, you will end up with one syscall per server. The servers themselves are doing more syscalls to cope with the smaller incoming requests, and their capacity drops.

This is not an insurmountable problem. It's possible with many clients to group related keys together. IE; all keys for a particular user's profile would end up on the same memcached instance, so a multiget will hit a single server with all of its keys and use far fewer syscalls in total.

##### A Well Designed Binary Protocol Client

Using spymemcached as an example; the client may take many application threads and use a single TCP connection back to memcached. With the binary protocol, it is possible to pack requests from different client instances into the same TCP socket, then dole back results to the right owners.

This means an application server, could in theory only require a single TCP socket per memcached instance. If you run 50 threads or processes per application node, this can vastly reduce overhead from large installs.

##### Remember Moore's Law

If many large sites were using the same hardware they had in 2007 for new memcached instances, they could have four times or more the server count than they do now. As years pass memory becomes cheaper and network capacity increases. Hardware increases won't necessarily stay behind your growth curve, but they do take a huge edge off as you can in theory halve your server count every few years.

##### Economy of Scale

I will also briefly note that massive installations attract better talent. The reality of the situation is much more likely to involve domain experts if your traffic is massive. The larger the cluster, the weirder the issues you run into, the more likely you are to have a team of people involved in maintaining it. These are extreme cases for extremely large clusters requiring guaranteed performance. Most users will have occasional problems to overcome, but not enough to warrant hires.