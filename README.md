# SIMH v4.0 - Beta

## WHAT'S NEW

### New Simulators

#### Matt Burke has implemented new VAX model simulators:
    VAX/11 730
    VAX/11 750
    VAX 8600/8650
    MicroVAX I
    MicroVAX II
    rtVAX 1000 (or Industrial VAX 620)
    
#### Howard Harte has implemented a Lincoln Labs TX-0 simulator.

### New Functionality

#### VAX/PDP11 Enhancements
    RQ has new disk types: RC25, RCF25, RA80
    RQ device has a settable controller type (RQDX3, UDA50, KLESI, RUX50)
    RQ disks default to Autosize without regard to disk type
    DMC11 DDCMP DECnet device simulation from Rob Jarratt.  Up to 4 DMC11 devices are supported.
    DZ on Unibus systems can have up to 256 ports (default of 32), on 
        Qbus systems 128 port limit (default of 16).
    DZ devices optionally support full modem control (and port speed settings 
        when connected to serial ports).
    DHU11 (device VH) on Unibus systems now has 16 ports per multiplexer.
    MicroVAX 3900 and MicroVAX II have SET CPU AUTOBOOT option

#### Terminal Multiplexer additions
    Added support for TCP connections using IPv4 and/or IPv6.
    Logging - Traffic going out individual lines can be optionally logged to 
            files
    Buffering - Traffic going to a multiplexor (or Console) line can 
            optionally be buffered while a telnet session is not connected
            and the buffered contents will be sent out a newly connecting 
            telnet session.  This allows a user to review what may have 
            happened before they connect to that session.

    Serial Port support based on work by J David Bryan and Holger Veit
    Serial Console Support
    Separate TCP listening ports per line
    Outgoing connections per line (virtual Null Modem cable).

#### Asynchronous I/O
    * Disk and Tape I/O can be asynchronous.  Asynchronous support exists for pdp11_rq, pdp11_rp and pdp11_tq devices (used by VAX and PDP11 simulators).
    * Multiplexer I/O (Telnet and/or Serial) can be asynchronous.  Asynchronous support exists for console I/O and most multiplexer devices.

#### Disk Extensions
    RAW Disk Access (including CDROM)
    Virtual Disk Container files, including differincing disks

#### Embedded ROM support
    Simulators which have boot commands which load constant files as part of 
    booting have those files imbedded into the simulator executable.  The 
    imbedded files are used if the normal boot file isn't found when the 
    simulator boots.  Specific examples are:  VAX (MicroVAX 3900 - ka655x.bin), 
    VAX860 (VAX 8600 - vmb.exe), VAX780 (VAX 11/780 - vmb.exe), 
    VAX750 (VAX 11/750 - vmb.exe), VAX730 (VAX 11/730 - vmb.exe), 
    VAX610 (MicroVAX I - ka610.bin), VAX620 (rtVAX 1000 - ka620.bin), 
    VAX630 (MicroVAX II - ka630.bin)

#### Control Flow

The following extensions to the SCP command language without affecting prior behavior:

         GOTO <Label>                 Command is now available.  Labels are lines in which the first non whitespace character is a �:�.  The target of a goto is the first matching label in the current do command file which is encountered.  Since labels don�t do anything else besides being the targets of goto�s, they could be used to provide comments in do command files, for example (�:: This is a comment�)
         SET ON                       Enables error trapping for currently defined traps (by ON commands)
         SET NOON                     Disables error trapping for currently defined traps (by ON commands)
         RETURN                       Return from the current do command file execution with the status from the last executed command
         RETURN <statusvalue>         Return from the current do command file execution with the indicated status.  Status can be a number or a SCPE_<conditionname> name string.
         ON <statusvalue> commandtoprocess{; additionalcommandtoprocess}
                                      Sets the action(s) to take when the specific error status is returned by a command in the currently running do command file.  Multiple actions can be specified with each delimited by a semicolon character (just like breakpoint action commands).
         ON ERROR commandtoprocess{; additionalcommandtoprocess}
                                      Sets the default action(s) to take when any otherwise unspecified error status is returned by a command in the currently running do command file.  Multiple actions can be specified with each delimited by a semicolon character (just like breakpoint action commands).
         ON <statusvalue>                   
         ON ERROR                     Clears the default actions to take when any otherwise unspecified error status is returned by a command in the currently running do command file.


Error traps can be taken for any command which returns a status other than SCPE_STEP, SCPE_OK, and SCPE_EXIT.   

ON Traps can specify any status value from the following list: NXM, UNATT, IOERR, CSUM, FMT, NOATT, OPENERR, MEM, ARG, STEP, UNK, RO, INCOMP, STOP, TTIERR, TTOERR, EOF, REL, NOPARAM, ALATT, TIMER, SIGERR, TTYERR, SUB, NOFNC, UDIS, NORO, INVSW, MISVAL, 2FARG, 2MARG, NXDEV, NXUN, NXREG, NXPAR, NEST, IERR, MTRLNT, LOST, TTMO, STALL, AFAIL.  These values can be indicated by name or by their internal numeric value (not recommended).

Interactions with ASSERT command and �DO �e�:
DO �e		is equivalent to SET ON, which by itself it equivalent to �SET ON; ON ERROR RETURN�.
ASSERT		failure have several different actions:
       If error trapping is not enabled then AFAIL causes exit from the current do command file.
       If error trapping is enabled and an explicit �ON AFAIL� action is defined, then the specified action is performed.
       If error trapping is enabled and no �ON AFAIL� action is defined, then an AFAIL causes exit from the current do command file.

Other related changes/extensions:
� The �!� command (execute a command on the local OS), now returns the command�s exit status as the status from the �!� command.  This allows ON conditions to handle error status responses from OS commands and act as desired.


#### Help

    HELP dev
    HELP dev ATTACH
    HELP dev REGISTERS

#### Generic scp support Clock Coscheduling as opposed to per simulator implementations.

#### New SCP Commands:

    SET ENVIRONMENT Name=Value      Set Environment variable
    SET ASYNCH                      Enable Asynchronous I/O
    SET NOASYNCH                    Disable Asynchronous I/O
    SET VERIFY                      Enable commang display while processing DO command files
    SET NOVERIFY                    Enable commang display while processing DO command files
    SET MESSAGE                     Enable error message output when commands complete (default)
    SET NOMESSAGE                   Disable error message output when commands complete
    SET QUIET                       Set minimal output mode for command execution
    SET NOQUIET                     Set normal output mode for command execution
    SET PROMPT                      Change the prompt used by the simulator (defaulr sim>)
    SET THROTTLE x/t                Throttle t ms every x cycles
    SHOW FEATURES                   Displays the devices descriptions and features
    SHOW ASYNCH                     Display the current Asynchronous I/O status
    SHOW SERIAL                     Display the available and/or open serial ports
    SHOW ETHERNET                   Display the available and/or open ethernet connections
    SHOW MULTIPLEXER                Display the details about open multiplexer devices
    SHOW CLOCKS                     Display the details about calibrated timers
    SHOW ON                         Display ON condition dispatch actions
    SET ON                          Enable ON condition error dispatching
    SET NOON                        Disable ON condition error dispatching
    GOTO                            Transfer to lable in the current DO command file
    CALL                            Call subroutine at indicated label
    RETURN                          Return from subroutine call
    SHIFT                           Slide argument parameters %1 thru %9 left 1
    NOOP                            A no-op command
    ON                              Establish or cancel an ON condition dispatch
    CD                              Change working directory
    SET DEFAULT                     Change working directory
    PWD                             Show working directory
    SHOW DEFAULT                    Show working directory

#### Command Processing Enhancements

##### Environment variable insertion
Built In variables %DATE%, %TIME%, %CTIME%, %STATUS%, %TSTATUS%, %SIM_VERIFY%, %SIM_QUIET%, %SIM_MESSAGE%
Command Aliases

   Token "%0" expands to the command file name. 
   Token %n (n being a single digit) expands to the n'th argument
   Tonen %* expands to the whole set of arguments (%1 ... %9)

   The input sequence "\%" represents a literal "%", and "\\" represents a
   literal "\".  All other character combinations are rendered literally.

   Omitted parameters result in null-string substitutions.

   A Tokens preceeded and followed by % characters are expanded as environment
   variables, and if one isn't found then can be one of several special 
   variables: 
   
          %DATE%              yyyy/mm/dd
          %TIME%              hh:mm:ss
          %CTIME%             Www Mmm dd hh:mm:ss yyyy
          %STATUS%            Status value from the last command executed
          %TSTATUS%           The text form of the last status value
          %SIM_VERIFY%        The Verify/Verbose mode of the current Do command file
          %SIM_VERBOSE%       The Verify/Verbose mode of the current Do command file
          %SIM_QUIET%         The Quiet mode of the current Do command file
          %SIM_MESSAGE%       The message display status of the current Do command file
          
   Environment variable lookups are done first with the precise name between 
   the % characters and if that fails, then the name between the % characters
   is upcased and a lookup of that valus is attempted.

   The first Space delimited token on the line is extracted in uppercase and 
   then looked up as an environment variable.  If found it the value is 
   supstituted for the original string before expanding everything else.  If 
   it is not found, then the original beginning token on the line is left 
   untouched.

##### Command aliases
   commands can be aliases with environment variables.  For example:
   
      sim> set env say=echo
      sim> say Hello there
      Hello there

##### Do command argument manipulation

The SHIFT command will shift the %1 thru %9 arguments to the left one position.


### Use Prebuilt Windows Simulators

Simulators for the Windows platform are built and made available on a regular basis (at least once a week if changes have been made to the codebase).  

The prebuilt Windows binaries will run on all versions of Microsoft Windows from Windows XP onward.

They can be accessed at https://github.com/simh/simh-Win32-Download

Several relatively recent versions should be available which you can download and use directly.

### Building simulators yourself

First download the latest source code from the github repository's master branch at https://github.com/simh/simh/archive/master.zip

Depending on your host platform one of the following steps should be followed:

#### Linux/OSX other *nix platforms

If you are interested in using a simulator with Ethernet networking support (i.e. one of the VAX simulators or the PDP11), then you should make sure you have the correct networking components available.  The instructions in https://github.com/simh/simh/blob/master/0readme_ethernet.txt describe the required steps to get ethernet networking components installed and how to configure your environment.

See the 0readme_ethernet.txt file for details about the required network components for your platform.  Once your operating system has the correct networking components available the following command will build working simulators:

   $ make {simulator-name (i.e. vax)}

#### Windows

Compiling on windows is supported with recent versions of Microsoft Visual Studio (Standard or Express) and using GCC via the MinGW environment.  Things may also work under Cygwin, but that is not the preferred windows environment.  Not all features will be available as well as with either Visual Studio or MinGW.

##### Required related files.  The file https://github.com/simh/simh/master/Visual%20Studio%20Projects/0ReadMe_Projects.txt

##### Visual Studio (Standard or Express) 2008, 2010 or 2012

The file https://github.com/simh/simh/master/Visual%20Studio%20Projects/0ReadMe_Projects.txt describes the required steps to use the setup your environment to build using Visual Studio.

##### MinGW

The file https://github.com/simh/simh/master/Visual%20Studio%20Projects/0ReadMe_Projects.txt describes the required steps to use the setup your environment to build using MinGW.

#### VMS

Download the latest source code as a zip file from: https://github.com/simh/simh/archive/master.zip

Unzip it in the directory that you want SIMH to reside in.  Unpack it and 
set the file attributes as follows:

$ unzip simh-master.zip
$ set default [.simh-master]
$ set file/attri=RFM:STM makefile,*.mms,[...]*.c,[...]*.h,[...]*.txt

Simulators with ethernet network devices (All the VAX simulators and the 
PDP11) can have functioning networking when running on Alpha or IA64 OpenVMS.

In order to build and run simulators with networking support, the VMS-PCAP 
package must be available while building your simulator.  The simh-vms-pcap.zip 
file can be downloaded from https://github.com/simh/simh/archive/vms-pcap.zip   
This link will return a file called simh-vms-pcap.zip which should be unpacked as follows:

$ unzip -aa simh-vms-pcap.zip
$ rename [.simh-vms-pcap]pcap-vms.dir []

The PCAP-VMS components are presumed (by the descript.mms file) to be 
located in a directory at the same level as the directory containing the 
simh source files.  For example, if these exist here:

[]descrip.mms
[]scp.c
etc.

Then the following should exist: 
[-.PCAP-VMS]BUILD_ALL.COM
[-.PCAP-VMS.PCAP-VCI]
[-.PCAP-VMS.PCAPVCM]
etc.

To build simulators:

On a VAX use:

$ MMx

On a Alpha & IA64 hosts use:

$ MMx                        ! With Ethernet support
$ MMx/MACRO=(�NONETWORK=1)   ! Without Ethernet support

UNZIP can be found on the VMS freeware CDs, or from www.info-zip.org
MMS (Module Management System) can be licensed from HP/Compaq/Digital as part of the VMS Hobbyist program (it is a component of the DECSET product).
MMK can be found on the VMS freeware CDs, or from http://www.kednos.com/kednos/Open_Source/MMK
DEC C can be licensed from HP/Compaq/Digital as part of the VMS Hobbyist program.