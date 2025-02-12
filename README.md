java c
CIS   5530:   Project   1 Code   Documentation
Link   State   and   Distance   Vector   Routing
Spring   2025
Overview
In this assignment, you will implement two routing protocols:   link state and   distance   vector routing.
We   will   be   using   the ns-3   discrete   network   simulator   to   teach   core   principles   of   network   routingprotocol   design   and   implementation.         Your   assignment   is   to   extend   ns-3   to   support   efficient   routing   using   link-state   and   distance-vector   protocols.    An   important   goal   of this   project   is   to   provide   you   the   opportunity   to   read   and   understand   a   sizable   piece   of   software   and   extend   it.   Hence, we   have   deliberately not included all the details about the ns-3   code,particularly on   specific APIs.Please be aware that no amount of   documentation can replace actual reading and running of   the code   itself.      So rather than   spend hours   digesting this   document   without   looking   at   the   code,   make   sure   you   treat this   document   as   a reference   guide   while you run   the   simulator   and   step   through   the   control   flow   of various interacting   software modules.   To get into the   habit   of working   as   a   team,   we   encourage   you   to   spend a day or two to get   your   entire   group   together   to   try   to   understand   the   code   as   a   team   and   help   each other   out.
1          Getting   Started
1.1            Container
For   a   consistent   environment, you   can   use   the   Docker   configurations   provided   here:   https://github.com/CIS-   5530/DockerEnv.   Follow the   directions in that repo’s README.md.
1.2          Git   RepositoriesWe   will   be   setting   up   a   git   repo   for   you   in   the   CIS-5530   Github   organization   with   all   of   the   starter   code preloaded.   Do not   set up your own git repo outside   of our   assigned repositories.   We   will   post   a   Google   form. for you to enter your group’s information for creating your repositories.
2          Code   Structure   and   CompilationFor   the   purpose   of   this   project,    we   have   isolated   all   the   code   that   you   need   to   make/learn   within   the   contrib/upenn-cis553   folder.       All   the   other   files   for   the   purpose   of   testing   are   stored   under   scratch/results, scratch/scenarios and scratch/topologies folder.One   of the important things to learn   from   this   project   is   learning   how   to   read   and   understand   APIs   within   the   code,   before   filling   in   the   actual   implementation.   We   will   not provide   details   of APIs here.   Understanding these APIs is part of your project.   To help you understand each function, we have added   some comments (function headers).
2.1          Files   to   modify
•      ls-routing-protocol   .cc
This   file   contains   all   the   event   handles   that   you   need   to   implement   for   handling   incoming/outgoing route messages.
–    For   each   incoming   message, you   need   to   implement   the   logic   to   handle   the   message, update   local routing tables, and send out outgoing   messages.
–    Periodic/triggered link-state updates should be transmitted and handled within this class.
–    Once   the   routing   table   is   computed,   handlers   for   forwarding   messages   should   be   imple-   mented   here,i.e., given   an   incoming   message, forward   the   message   along   the   computed   next
hop to the destination.
•    ls-message   .ccThis   file   implements   all   the packet   formats   used   in   the   above   file.    To   implement   your   link-state   protocol,   feel free to extend this   file   to   add   new   packet   formats,   for   instance,   “hello”   packets   for   neighbor   discovery, and   “lsa” for   link-state   advertisements.
•      dv-routing-protocol   .cc
Similar to ls-routing-protocol.cc
•    dv-message   .cc
Similar to ls-message   .cc
2.2          Files   that   are   provided
•      upenn-cis553/test-app
This folder contains the autograder and test   code.
–      test-app   .ccThis   is   an   example   application   that   we   have   provided   to   test   your   link-state   or   distance   vector implementation.      This   application   periodically   generates   a   small   packet   to   be   transmitted   from any source to   a   destination   node.Feel   free   to   modify   test-app   .cc to   include   your   tests.    While   debugging   your   code,   it   is   advisable   to   modify   our   test-app   .cc to   do   something   simpler,   for   instance,   sending just   one message between a fixed source   and   destination.
•    upenn-cis553/common
This   folder   contains   all   logging   wrappers   and   test   essentials.      You   should   not   modify   the   files   inside this folder
•    scratch/topologies
This   folder   contains   the   input   network   to   the   ns-3 simulation.
•    scratch/scenarios
This folder contains the   step-by-step   scenario file that   you   can   customize   to   start/stop   a   network,   bring   up/down   links, output   network   state, etc.
•      scratch/results
This folder contains the   sample   output for   ‘DUMP      NEIGHBORS’,   ‘DUMP      ROUTES   ’.
•    scratch/simulator-main   .ccThis   contains   the   main   driver   program   for   your   simulation.      It   takes   as   input   the   topology   and   scenario file, and executes the   scenario.   In the   process,   commands   from   the   scenario   file   are   sent   to   ls-routing-protocol   .cc and dv-routing-protocol.cc, for instance, to generate a table   dump,   etc.   Basically,   our   simulator-main handles basic   commands related to link/node   topology   changes,   and   redirect   routing-related   commands   to   other   modules.    For   instance,   routing-related   commands   are   sent   to   ls-routing-protocol.cc and   dv-routing-protocol.cc,   while   test-   case-specific commands are sent to test-app   .cc.   This file also generates optional outputs traces   for animation.
DO NOT MODIFY   simulator-main   .cc.
Note:    upenn-cis553/keys   and   upenn-cis553/penn-search   are   used   in   Project   2,   you   should   be able   to   finish   Project   1 without   modifying   them.
2.3          CompilationTo   compile   the   code,   first   ssh   to   the   docker   environment.    We   use   ‘waf’, which   is   a   python-based   build
tool   to   build   our   project.   In   order   to   use   it, first   make   it   executable.
$   chmod   +x   waf
Then run   the   configure   command.    You   only   need   to run   it   once,   unless   you   changed   the   wscript   and   thus   need   to   update   the   configure.
$   ./waf   configure
Then,   compile   the   code   using   the   following   command,   it   will   generate   a   simulator-main   exe-   cutable at   ./build/scratch.   It takes quite some time for the first time, and will   be   faster   later.
$   ./wafIf you want to add new helper   source files, for   instance,   creating   your   own   classes   for   route   entries,   neighbor   entries, etc.   Whenever   you   add   a   new   file, you   need   to   modify   the   wscript   file   (most   likely   to be   contrib/upenn-cis553/wscript) within the   subdirectory where your new file resides.   Failure   to   do   this   means   that   the   new   file   may   be   excluded   from   the   build.For   faster   compilation,   use   ‘   ./waf    -j    4’   to   enable   parallel   compilation.    Please   do   not   be   greedy   and increase 4 to   a larger number,   since   that   will   slow   down   the   virtual   machine   and   may   not   speed   up your compilation by much!
2.4          Running   and   interacting   with   the   simulator
The compiled binaries are located in the build directory.   Now that you have successfully   compiled your   code, you   can   run   the   simulator   by   going   to   the   main   ns-3   directory,   and   run   the   command   below:
$   ./waf   --run   "simulator-main   --routing=〈LS/DV/ANY/NS3〉\
                                                                                       --scenario=〈scenario-file〉\
                                                                                       --inet-topo=〈topology-file〉"Note:   ‘waf’ is one of the build tools that   have   good   source   file   management,   however,   you   can   also   run the code as a normal C++   project   if you like.   Remember   to   set   environment   variable   first   (you   only   need   to   do   it   once   every   time   you   open   a   terminal   session):
$   export   LD_LIBRARY_PATH=./build/lib/
And   then   run:
$   ./build/scratch/simulator-main   --routing=〈LS/DV/ANY/NS3〉\
                                                                                               --scenario=〈scenario-file〉\
                                                                                                --inet-topo=〈topology-file〉You   can   choose   either   way   of   running   your   project, and   the   same   rule   applies   to   the   following   commands.   To test the implementation, you can add ‘–result-check’   argument. But you don’t need it   until   you   have   finished your implementation.
$   ./waf   --run   "simulator-main   --routing=〈LS/DV/ANY/NS3〉\
--scenario=〈scenario-file〉\
--inet-topo=〈topology-file〉\
--result-check=〈result-file〉"
For   example, if   you   want   to   test   LS   algorithm   with   the   10 nodes   topology:
$   ./waf   --run   "simulator-main   --routing=LS   \
--scenario=scratch/scenarios/10-ls.sce \
--inet-topo=scratch/topologies/10.topo   \
--result-check=scratch/results/10-ls   .output"
To understand the commands above, run
$   ./waf   –run   "simulator-main   –help"
3          NS-3 ArchitectureFor   the   most   part, you   do   not   need   to   read   any   additional   files   beyond   those   in   upenn-cis553.   However,   for   debugging purposes,   it   is   important   to   understand   the   interactions   of your   code   with   other parts   of   ns-3.   We   provide   a   high-level   overview   here.The   figure   shows   layers   2-5 from   the   perspective   of   a   single   ns-3   node.   At   the   link   layer,   each   node has   multiple   IP   addresses   and   interfaces/device.   At   the   IP   stack   (layer   3),   IP packets   are   forwarded   as   follows:
•    ‘RouteInput()’ receives   a   message   from   one   of t代 写CIS 5530: Project 1 Code Documentation Link State and Distance Vector Routing Spring 2025C/C++
代做程序编程语言he   devices.    Your implementation has   to   deter-   mine the next hop to betaken for this message and forward   the message to the appropriate   device   or   the   local   host   (if   the   destination   is   itself).
•    ‘RouteOutput()’ takes messages that originated from the local node, and then performs   a   similar   next-hop   forwarding   or   to   the   local   host   (if   the   destination   is   itself).
To   understand   how   ‘RouteInput()’   and   ‘RouteOutput()’   are   used,   consult   the   OLSR   code   in   sr-   c/olsr.   You   do   not   need   to   implement   multicast   for   this   project.
Note   that   all   control   messages   used   in   link-state   and   distance-vector   are   sent   via   UDP   protocol   in   layer   5 to   immediate   neighbors.   This   is   counter-intuitive   at   first, since   these   protocols   are   implemented   in layer   3, while   UDP   is   a   layer   4   service.   However, this   is   a   common   practice   to   utilize   the   UDP   protocol to   bootstrap   the   protocol   itself,   where   UDP   is   initially   used   for   communicating   with   direct   neighbors   
within the   same   subnet for exchanging   control messages.    However,   once your   routing   protocol   works,   you   should be   able to write   an   application that uses   UDP   sockets   to   send   packets   to   a   destination   node   that   is   multiple   hops   away.
4          Input   Topology
Your   ns-3 simulation   has   to   take   in   an   initial   input   point-to-point   topology.   To   see   an   example   topology,   refer to scratch/topologies.   Here is an example ten-node topology:
10   9
0   5   5
1   8   5
2   5   0
3   2   5
4   2   0
5   6   8
6   5   8
7   8   0
8   8   2
9   4   8
0   1   1973
0   2   4253
0   3   5341
0   5   4066
0   6   2702
0   9   4393
1   8   1033
2   7   10589
3   4   1126This   is   based   on   a   standard   topology   format   used   in   network   simulations.   The   topology   has   10 nodes   and   9   edges   as   initialized   in   the   first   line.    The   first   10 lines   list   each   node   and   two   attributes   (X   and   Y coordinates).   The   next   9 are   edges   connecting   nodes   A   to   B   with   a   edge   weight.   In   our   project,   all   edge weights are   set to   1,   so you can ignore the   edge   attribute too   (as   a   fun   exercise   after   the   project   is   done,   you can consider using this edge attribute to   do   shortest   path   by   aggregate   link   costs   rather   than   by   hop   count).The figure   shows   an example of point-to-point topology.   Each node   has   a   set   of neighbors,   one   for   each   of its   interfaces/device.    The   figure   above   contains   also   subnets   and   masks,   which   you   need   not   worry about for this project.   However, we included them in case you   are interested to   learn   more.In   the   topology   file,   we   refer   to   nodes   by   node   numbers   (e.g.,   0,    1,   2,    . . .   ).      However,   once   the topology   is   initialized,   our   simulator-main   assigns   to   each   node   multiple   IP   addresses.    The   reason   why each node requires multiple IP addresses is that it participates in multiple   subnets   (which you   need   not   worry   about).      Our   simulator-main   will   select   one   of   the   IP   addresses   as   the   unique   identifier   of   the   node.    This   identifier   is   what   the   node   should   advertise   to   its   neighbors   for   computing   routes.    For   instance,   consider   a   node   0 that   has   IP   addresses   IP0,   1   ,   IP0,2   ,   and   IP0,3   .   In   this   case,   IP0,   1    is   selected   as the   unique   identifier.    We   have   also   provided   APIs   that   will   map   from   the   logical   node   number   to   the   corresponding unique IP   address   (and vice versa).    However,   we have   included   this   discussion   here   for   your own understanding should you be curious to learn how addressing   works   in   a pt-to-pt   network.
5          Scenario   File
After the simulator has started the network, the   scenario   file is   used   to   generate   network   events,   such   as   link failures, node additions/failures, sending messages, dump commands to   display network   state,   etc.We have provided   an example   scenario file   at   scratch/scenarios/test.sce.    This   scenario file   contains most   of the   commands indicated below.    Your   goal is to   read   this   scenario   file   and   understand   what   it   is   doing.   For   your   own   testing   purposes, you   probably   should   write   your   own   (simpler) scenarios initially,   and feel free to   add your   own   commands   (for   example,   commands to   dump   link-state   updates   and network statistics).
Most of the common commands have the following format:         
‘   〈   Node    Number〉 〈   Module    Name〉 〈   Command〉 〈Arguments〉’
where ‘〈Node    Number〉’ refers to a node   in the   simulator   (0,   1,   2,   . . .   ).‘   〈   Module      Name〉’   refers   to   the   protocol/module, e.g., LS, DV, or   the   application   (e.g., test-app.cc).   Our   simulator-main   .cc will direct the command based   on the   module   name   to   the   appropriate   code   that implements the command handles.    (ls-routing-protocol   .cc,   dv-routing-protocol   .cc,   or   test-app.cc).
‘   〈   Command〉’ is the command to be sent to the node, e.g., ‘VERBOSE   ’   is to turn on debugging messages,   and PING is to send ping messages to   another node.
‘   〈Arguments〉’ is   any   additional   arguments   required   specific   to   the   command.
For   instance,   the   command   ‘1    LS    VERBOSE    TRAFFIC    ON’    will   result    in   node    1   turning    on   all   the   traffic   traces   for   the   link-state   protocol.    ‘*    LS    VERBOSE    TRAFFIC    ON   ’ will   turn   on   the   link-state   traces   for all   nodes.
•    ‘VERBOSE       〈   TYPE〉 〈ON/OFF〉’ Turn on debugging   messages.
‘   〈   TYPE〉’
Usage
‘TRAFFIC   ’
Use this when data is   sent/received
‘ERROR   ’
Use this when error messages are to be printed
‘DEBUG   ’
Use this to print debug logs
‘STATUS   ’
Use this to print status messages
‘OUTPUT   ’
Use this to write output   to   a   file
‘ALL   ’
Use this to switch   all traces   at   once
Note:   ‘ERROR’   and ‘STATUS   ’ verbose is ‘ON   ’   by default.
For   example, to   switch   on   ‘TRAFFIC’ traces   for   node   1:
1   LS   VERBOSE   TRAFFIC   ON
•    ‘PING       〈   NODE〉 〈   MESSAGE〉’   Send PING to   a   node.
Note   that   for   LS   and   DV   modules, PING   can   only   be   sent   to   immediate   neighbors. We   have   added this functionality as an example for students to implement   their   neighbor   discovery   for   MS   1.
On   the   other   hand, the   APP   module   PING   is   multi-hop, which   means   the   PING   message   ought   to   be forwarded to the destination node using the routing tables computed by LS or DV.
For   example, Node   1 sends   PING   request   to   node   2 with   a   message:   “hi!”:
1   LS   PING   2   hi!
You   may   also   use   “*” wildcard   with   APP   module:
1 APP   PING   *   “hello!”
Node   1 sends   PING   to   all   the   nodes   in   the   topology.
•    ‘NODELINKS       〈UP/DOWN〉 〈NODE    NUMBER〉’ Bring   up/down   all   links   of a   node.   Brings   down   all   links   on   node   1.
NODELINKS   DOWN    1
•    ‘LINK       〈UP/DOWN〉 〈NODE_A〉〈   NODE_B〉’ Brings up/down   all links   between   ‘NODE      A’   and   ‘NODE      B   ’   Bring   down   link(s) between   node   1 and   8.
LINK   DOWN    1   8
•    ‘LINK       〈UP/DOWN〉〈LINK    NUMBER〉’ Bring up/down   a   specific link   as   defined in   topology   file.   Bring   down   7th   link   defined   in   topology   file.
LINK   DOWN   6
•    ‘TIME      〈   MILLISECONDS〉’ Advance   scenario file time before next   command.   Advance   time   by   100 milliseconds.
TIME   100
•    ‘TIME   ’   Display   the   current   simulator   time
•    ‘QUIT’   Quit   Simulator
5.1          Commands   to   Implement
Note that the commands you need to implement for our actual   demo   are   as follows:
Command
Remarks
Example
‘DUMP      ROUTES   ’
Print out routing table
‘   1      LS/DV    DUMP      ROUTES   ’
‘DUMP      NEIGHBORS   ’
Print out neighbors
‘   1    LS/DV      DUMP      NEIGHBORS   ’
5.2          Interactive   Scenario   ModeIn   addition   to   using   the   scenario   file,   you   can   also   enter   the   scenario   commands   interactively   via   the   keyboard.    While   the   simulation   is   running,   there   is   a   command   prompt   that   you   can   use   to   enter   the   commands described above.
6          Auto-checker   ModeAt   some point before your actual demonstration, we will be   providing   you   with   a   sample   topology/sce-   nario   and   a   result-check   file.   The   auto-checker   mode   can   be   invoked   by   providing   the   given   topology/s-   cenario files, together with the result-check   file,   by   using   the   option   ‘--result-check=〈   filename〉’   to   the   ‘simulator-main   ’.    Our   auto-checker   would   then   compare   your   implementation’s   output   with   our   reference implementation’s   output   (provided in result-check   file).    To make   this   comparison   work,   you   must   call   two   “hook” methods   in   your   code.   If   you   look   into   the   ‘DumpNeighbors()’ and   ‘DumpRouting-   Table()   ’ functions, you will find commented function   calls to   ‘checkNeighborTableEntry(   .   .   .)’    and   ‘checkRouteTableEntry(   .   .   .)’, respectively.   After you get your implementation   running,   uncomment   these   function   calls   and pass   on the required   arguments   (declarations   are in   upenn/test-result.h).   Once these hooks are properly setup, the auto-checker would pinpoint the inconsistencies between your   implementation and the reference implementation, if any.Please   note   that   having   your   implementation   match   our result-check   file   is   the bare   minimum   that   you   should   aim   to   achieve.    We   recommend   that   you   make   sure   your   implementation   clears   the   auto-   checker   before   submitting   on   the   Gradescope   auto-grader.   We   are   providing   you   this   file   so   that   you   can be sure that your implementation is on   track.



         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
