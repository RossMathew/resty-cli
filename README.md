Name
====
Command-line utility for OpenResty.


Description
===========

The "resty" script invokes the "nginx" executable, 
disabling daemon, master_process, access_log, and other things it does 
not need. No server {} is configured hence no listening sockets 
involved. The Lua code is initiated by the init_worker_by_lua 
directive and run in the context of ngx.timer callback. So all of 
ngx_lua's Lua APIs available in the ngx.timer callback context are 
also available in the "resty" utility. We may remove some of the 
remaining limitations in the future though :) 


Synopsis
========

    $ export PATH=/usr/local/openresty/bin:$PATH 

    $ which resty 
    /usr/local/openresty/bin/resty 

    $ resty -h 
    /usr/local/openresty/bin/resty [-h] [-c num] [-e prog] [-V] [lua-file] 

    Options: 
       -c num      set maximal connection count (default: 64). 
       -e prog     run the inlined Lua code in "prog". 
       -h          print this help. 
       -V          print the underlying nginx version and configurations. 

    For bug reporting instructions, please see: 
    <http://openresty.org/#Community> 

    $ resty -e 'print("hello")' 
    hello 

    $ time resty -e 'ngx.sleep(3) print("done\n")' 
    done 

    real 0m3.085s 
    user 0m0.071s 
    sys 0m0.010s 

    $ resty -e 'ngx.say(ngx.md5("hello"))' 
    5d41402abc4b2a76b9719d911017c592 

    $ resty -e 'io.stderr:write("hello world\n")' > /dev/null 
    hello world 

I've also tried the code example in lua-resty-mysql's doc's "Synopsis" 
section which works out of the box: 

    $ resty mysql-example.lua 
    connected to mysql. 
    table cats created. 
    3 rows inserted into table cats (last insert id: 1) 
    result: [{"name":"Bob","id":"1"},{"name":"","id":"2"},{"name":null,"id":"3"}] 
    where a.lua is from lua-resty-mysql's docs. 

Stdin also works (in addition to stdout and stderr): 

    $ resty -e 'print("got: ", io.stdin:read("*l"))' 
    hiya 
    got: hiya 

where the first "hiya" line was entered from my keyboard ;) 

"Light theads" also work: 

    $ time resty -e 'local ths = {} 
                           for i = 1, 3 do 
                               ths[i] = ngx.thread.spawn(function () 
                                               ngx.sleep(3) ngx.say("done ", i) 
                                           end) 
                           end 
                           for i = 1, #ths do ngx.thread.wait(ths[i]) end' 
    done 1 
    done 2 
    done 3 

    real 0m3.073s 
    user 0m0.053s 
    sys 0m0.015s 

Authors
=======

* Yichun Zhang (agentzh) <agentzh@gmail.com>, CloudFlare Inc.

* Guanlan Dai <guanlan@cloudflare.com>, CloudFlare Inc.



Copyright and License
=====================

This module is licensed under the BSD license.

Copyright (C) 2013-2014, by Yichun "agentzh" Zhang (章亦春) <agentzh@gmail.com>, CloudFlare Inc.
Copyright (C) 2013-2014, by Guanlan Dai <guanlan@cloudflare.com>, CloudFlare Inc.

All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
