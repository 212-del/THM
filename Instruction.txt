These instruction are meant for kali linux. These soulution will works better on kali linux.

# Step 1

First step is to launch the room from your tryhackme account.

Step 2 

Next step is how to connect to the launched room.
The Instruciton to connect to the room is clearly mentioned always in the room instruction.
If you get the ip there are 2 ways to do it.

way 1

Connection Via AttackBox

You will get a option in the room Webpage to launch a virtual envioroment to launch a machine that is already connected to the room. That machine will have some buildin tool.

Way 2

Another way to do it by connecting the room from your own machine
To do this here are the steps

  Step 1
    Do copy room ip and paste it into the file /etc/hosts.(hosts is a file.) if that file not exists make one. and save&exit.
  Step 2
    Go to your thm account and them edit the url with the url https://tryhackme.com/access then download the files under the       machines tab. save it to a location where you remember. lets say at ~/Desktop/<filename>.ovpn
  Step 3 
    Now install Openvpn into your local machine if not isntalled. Then run the vpn with the command suod openvpn <location of      that vpn file>
And Thats it now your local machine is connected to the room.


Point to notice: Now your local machine is connecting the room via  a different network interface that is tun0 and with another ip too. as well as room is only able to connect you via the ip listed under the interface tun0
You can see all ip related info by the command "ip a"
