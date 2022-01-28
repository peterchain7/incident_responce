# incident_responce
## Wireshark
 1. Finding frames containing some files, Example which contains an image 20210429_152157.jpg
    
        frame contains 20210429_152157.jpg
  2. Finding all serverf hellow ==> userfull when finding the server certificate public key
    * https://medium.com/@sshekhar01/cyberdefenders-packetmaze-beffc1d05cb
         
           tls.handshake.type==2
         
  3. Finding  all server names 
  
         ssl.handshake.extensions_server_name
    
  4. Finding domain name if you have an ip address
     
      EXample question `What domain was the user connected to in packet 27300?`
      
         i. FIrst find the ip address of the server
         2. Go for statistics -> Resolved address & check for the domain name corresponding to the above destination address.(search for the ip address)
         3. 
## Scripting For Analysis
### find command
* https://thispointer.com/linux-find-files-larger-than-given-size/#:~:text=Size%20%3A%207.2G-,Find%20files%20larger%20than%201gb%20in%20Linux,1G%20in%20the%20find%20command.&text=It%20recursively%20searched%20for%20files%20inside%20the%20folder%20%E2%80%9C%2Fusr%2F,the%20paths%20of%20such%20files.

  The find command is used to search files and directories. You can search for files based on several criteria such as permission, modification/creation time, size, type etc. The syntax of the find command is as follows: `find [path...] [expression]`


    
                find . -iname pass ==> finds files in current directory abd a file containing keyword `pass`
In the day5 directory there is also a file called pass3. We can locate both the pass and pass3 files using the * character as shown below:

                [root@redbundi admin]# find /home/labs/day5 -name "pass*"  ==> will find all files starting with `pass`
                         /home/labs/day5/pass
                        /home/labs/day5/pass3
                        [root@redbundi admin]#

LOCATING DIRECTORIES
By using the 'type' option you can search for directories.

          find /usr/ -type d -name "lib"
          
The commonly used 'type' are:

       type f (default, no need to specify f)

       type d (search for directory)

       type p (search for named pipe FIFO)

       type l (search for symbolic link)

       type s (search for socket)

       Using the printf option

The default find output is do display the results (files/folders) with path next them. However, you can customize the output using printf. Valid options that can be used with printf are:

            %f - just print file name

            %p - display the full path to the file (default output)

            %m - display permission

            %g - print group

            %u - print user

            %h - just print the directory (no file name will be displayed)
   
Some example are:

          find /etc -name iptables -printf %f\\n
          iptables iptables
          find /etc -name iptables -printf %p\\n
          /etc/rc.d/init.d/iptables /etc/sysconfig/iptables
          find /etc -name iptables -printf %m\\n
          755 600
          find /etc -name iptables -printf %g\\n
          root root
          find /etc -name iptables -printf %h\\n
          /etc/rc.d/init.d /etc/sysconfig
          find /etc -name iptables -printf %u\\n 
          root root
          
          Find and send the output to next command
The -exec option used to send the output to next command.

           find /var/log -name messages -exec grep kernel {} \;
           
           Sep 20 09:52:59 ids-1 kernel: device eth0 entered promiscuous mode 
           Sep 20 09:53:01 ids-1 kernel: device eth0 left promiscuous mode
           
### GREP

* https://www.cyberciti.biz/faq/grep-regular-expressions/
* https://learnbyexample.github.io/learn_gnugrep_ripgrep/breere-regular-expressions.html

grep searches the input files for lines containing a match to a given pattern list. When it finds a match in a line, it prints the line to standard output (by default), or whatever other sort of output you have specified.

How it can help to analyze logs?

You can filter specific alert from snort alerts

Extract failed login attempts from secure/syslog

Analyze httpd logs (search for 403 error code …)

Redirect output to file or to another command

You can use regular expressions with grep to filter specific pattern in the above mentioned log files and/or any other files

Syntax:

            grep options pattern filename/filepath

Some of the options are:

              -i : ignore case sensitive

              -v : invert match

              -n : prefix line number with output

              -h : do not display file name

              -r : recursive search

              -l : just list the file names (matched line will not be printed)

              -e regex : specified pattern is regular expression format
              
 Print successful login attempts from /var/log/secure.


            [root@redbundi day5]# grep -i accepted /var/log/secure
            Oct 20 01:03:22 redbundi sshd[3694]: Accepted password for admin from 10.2.0.5 port 52554 ssh2
            Oct 20 01:07:19 redbundi sshd[3742]: Accepted password for admin from 10.2.0.4 port 1114 ssh2
            Jan  4 01:17:54 redbundi sshd[3739]: Accepted password for admin from 10.2.0.5 port 38400 ssh2
           
 Next, say you want to display all alerts that generated at specific time, (03:00 am).
 
          grep '02:58' snort.alerts
          02/23-06:02:58.539673 [**] [1:1852:4] WEB-MISC robots.txt access [**] [Classification: access to a potentially vulnerable web application
Grep with multiple patterns use `|`  to sepate patterns
Exampe 
  
      grep "Denied|Forbidden" /var/log/httpd/error_log   
 It will find all Denied and Forbidden words in error_log file `case sensitive`
 
Using a simple regular expression to print alerts that belongs to 'MySQL and MYSQL'

      grep -i -e 'M.SQL' snort.alerts
      
What if we need to know the number of events matched? We can pipe the above commands output to wc command


       [root@redbundi day5]# grep -i -e 'M.SQL' snort.alerts | wc -l
       486
       
### NGREP
ngrep (network grep), is similar to grep but it is used to search inside pcap files or through a live packet capture. We can search by specifying simple alpha-numeric patters, regular expressions or even hexadecimal patterns.

 Syntax:
 
     ngrep [options] pattern [filter] Options:

Most of the options are similar to tcpdump options, and you can use the expressions (bpf filters) with ngrep command.

            -w word : match the given regular expression as 'word’

            -i : ignore case

            -x : dump packets in ASCII and hex

            -d iface : specify interface to listen on

            -n number : capture/display number of packets and exit

            -F file : read the BPF filter from the file

            -v pattern : invert match, do not match the given pattern

            -s snaplen : specify the number of bytes to capture (default 65536 bytes)

            -I file : load pcap from ‘file’

            -O file : write pcap format into ‘file’

            -W : format of payload output

            -W normal : standard output

            -W byline : line by line output

            -W none : single line output

            -X pattern : consider the pattern as hexadecimal

            -p : do not run in promiscuous mode

            -t : print time stamp

            -q : quite mode, print only header and payload
            
 NGREP EXAMPLES
Open another terminal using the webshell and run the following commands.

Print all udp packets.

        ngrep -q udp
Print packets that contains word yahoo. On a new terminal, type `curl yahoo.com`

        [root@redbundi day5]# curl yahoo.com
        
        [root@redbundi day5]# ngrep -w yahoo -q -x
        interface: eth0 (192.168.125.0/255.255.255.0)
        filter: ((ip || ip6) || (vlan && (ip || ip6)))
        match: ((^yahoo\W)|(\Wyahoo$)|(\Wyahoo\W))

       U 192.168.125.100:57718 -> 8.8.8.8:53 #245
         2c f3 01 00 00 01 00 00    00 00 00 00 05 79 61 68    ,............yah
         6f 6f 03 63 6f 6d 00 00    01 00 01                   oo.com..... 
Monitor the email traffic and display sender and recipients using regular expressions;

     [root@redbundi admin]# ngrep -iq 'rcpt to|mail from' tcp port 25 
     
### AWK
The basic function of awk is to search files for lines (or other units of text) that contain certain patterns. AWK is an excellent tool for processing these rows and columns, and is easy to use. awk is extremely powerful but in this lab we will be looking only at the field separators, which is the most commonly used feature.

awk's basic syntax is as follows:

     awk options pattern {action}

Let's take a look at the first two rows from the snort.alerts file to explain some of the awk usages.

     cd /home/labs/day5
     head -2 snort.alerts
     02/21-13:10:27.138826 [**] [1:2010937:2] ET POLICY Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 222.73.236.65:54907 -> 192.168.1.2:3306
     02/21-13:10:28.198750 [**] [1:2010937:2] ET POLICY Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 222.73.236.65:54927 -> 192.168.1.2:3306
By default awk splits a row into a number of fields using space as a field separator. Each field can be referenced through the $ character followed by the field number. For instance, in order to print the first field from every row we can do as follows:

       awk '{print $1}' snort.alerts | head -2
       02/21-13:10:27.138826
       02/21-13:10:28.198750
The above command also uses the useful head command which allows to display only the a given number of the first line (the number of lines to be displayed is the parameter 2 in this case). While printing fields using awk, the print command and fields ($n) must be enclosed within '{…..}' .

awk also provides an option called -F to customize the field separator. For instance, let's use the colon : as a field separator and print the first field from our snort.alerts file.

       awk -F: '{print $1}' snort.alerts | head -2 
       02/21-13 
       02/21-13
The following command will print 2 fields using the field separator is :

      awk -F: '{print $1,$2}' snort.alerts | head -2 
      02/21-13 10 
      02/21-13 10
      
      
#### Other examples of AWK
   * https://www.geeksforgeeks.org/awk-command-unixlinux-examples/
      
 Sample Commands 

Example: 

Consider the following text file as the input file for all cases below: 

    $cat > employee.txt 
    ajay manager account 45000
    sunil clerk account 25000
    varun manager sales 50000
    amit manager account 47000
    tarun peon sales 15000
    deepak clerk sales 23000
    sunil peon sales 13000
    satvik director purchase 80000 
    
1. Default behavior of Awk: By default Awk prints every line of data from the specified file.  

        $ awk '{print}' employee.txt
      
Output:  

       ajay manager account 45000
       sunil clerk account 25000
       varun manager sales 50000
       amit manager account 47000
       tarun peon sales 15000
       deepak clerk sales 23000
       sunil peon sales 13000
       satvik director purchase 80000 
       
In the above example, no pattern is given. So the actions are applicable to all the lines. Action print without any argument prints the whole line by default, so it prints all the lines of the file without failure. 

2. Print the lines which match the given pattern. 

         $ awk '/manager/ {print}' employee.txt 
         
Output:  

      ajay manager account 45000
      varun manager sales 50000
      amit manager account 47000 
      
In the above example, the awk command prints all the line which matches with the ‘manager’

3. Splitting a Line Into Fields : For each record i.e line, the awk command splits the record delimited by whitespace character by default and stores it in the $n variables. If the line has 4 words, it will be stored in $1, $2, $3 and $4 respectively. Also, $0 represents the whole line.  

            $ awk '{print $1,$4}' employee.txt 
Output:  

           ajay 45000
           sunil 25000
           varun 50000
           amit 47000
           tarun 15000
           deepak 23000
           sunil 13000
           satvik 80000 
In the above example, $1 and $4 represents Name and Salary fields respectively. 
