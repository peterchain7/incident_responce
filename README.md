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
