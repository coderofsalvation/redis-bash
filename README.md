REDIS-BASH
==========
Singlefile Bash clientscript to access Redis, and 'init.d'-style channel-hooks   


### What

This is a singleshellscript which can write/get values to redis, as well as listening on channels.
Channelmessages will be automatically trigger scripts (if file ispresent in 'hooks' directory structure).
Perfect to use as a bash eventhandler on several servers, without having to need to install redis itself.. 

### Howto

A picture speaks a thousand words, and a bash command speaks a thousand pictures:

    $ ./redis-bash

    Usage: ./redis-bash <listen|send> [REDISCMD] [topic] [message]

      options:
        -h Host - Defaults localhost.
        -p Port - Defaults 6379.
        -n DB - Select the database DB.
        -r N - Repeat command N times.
        -a PASSWORD - Authentication password
        -i INTERVAL - Interval between commands
        -v verbose - verbose level (1=more, 2=mostest, 3=mosdef, handy for debugging)

      NOTE: the 'hook' directory contains all channels which are being listened to, scripts in 
            these directory will automatically be triggered.

      Examples: 
          ./redis-bash listen -h localhost -p 6379 
          ./redis-bash listen -h foo.com   -p 6379 -a mypassword           
          ./redis-bash send   -h foo.com   -p 6379 -a mypassword PUBLISH foo/bar 'this is a message'         
          ./redis-bash send   -h foo.com   -p 6379 -a mypassword -v 1 PUBLISH foo/bar 'this is a message'         
          ./redis-bash send   -h foo.com   -p 6379 -a mypassword SET     "Foo Users" 3
 
### Test output

Terminal #1:

    $ ./redis-bash listen -a mypassword
    connecting to localhost port 6379
    subscribing to channel 'foo/bar'
    subscribing to channel 'bar/foo'
    (..waiting..)

Terminal #2:

    $ ./redis-bash send -p 6379 -a mypassword SET foo bar
    OK
    $ ./redis-bash send -p 6379 -a mypassword GET foo
    bar
    $ ./redis-bash send -p 6379 -a mypassword PUBLISH foo/bar "this is a message"
    1
    $

Terminal #1 now outputs:

    -> message from: foo/bar                                  = this is a message..
       -> hooks/foo/bar/triggertest: Hi I just received 'this is a message'

### The jsonfiles

The jsonfiles are there to define the existing keys and channels.
The bashscript will check the hooks directory for scripts which listen to nonexisting channels or keys.
Also, it is handy to share json-files with other developers/partners e.g. as a (online) resource for developing.
(You could share the repo, and let others add channels to the json as the application grows, and allow others readonly-access
by symbolically liking the jsonfile to a webdirectory e.g.).

### Requirements 

* Only needed is bash4 with /dev/tcp compiled (almost always), therefore it is highly portable.


### Credits
* Andre Ferraz - Debian Package
* Juliano Martinez - Idea to handle socket disconnections on the pubsub demo

