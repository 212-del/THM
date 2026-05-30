🏷️ *Room Name =* **Brute-It**

📝 *Room Description =*



Learn how to brute, hash cracking and escalate privileges in this box!

In this box you will learn about:

- Brute-force

- Hash cracking

- Privilege escalation

Connect to the TryHackMe network, and deploy the machine.


 
❓ *Room Questions =*

| #   | Question                                                              | 💡 Hint                                                                                                                                                                                                          |
|-----|-----------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Q1  | Search for open ports using nmap.How many ports are open?                       			      | nmap -sS -sV MACHINE_IP                                                                                                                                                                                                         |
| Q2  | What version of SSH is running?                       			      | Nothing                                                                                    
| Q3  | What version of Apache is running?                       			      | Nothing                                                                                    
| Q4  | Which Linux distribution is running?                       			      | Nothing                                                                                    
| Q5  | Search for hidden directories on web server. What is the hidden directory?                       			      |  gobuster dir -u MACHINE_IP -w common.txt                                                                                    
| Q6  | What is the user:password of the admin panel?                       			      | Use hydra to brute force it!                                                                                    
| Q7  | Crack the RSA key you found. What is John's RSA Private Key passphrase?                       			      | use john to crack the private rsa file.                                                                                    
| Q8  | user.txt                       			      | Nothing                                                                                    
| Q9  | web flag                       			      | Nothing                                                                                    
| Q10  | Find a form to escalate your privileges. What is the root's password?                       			      | search for gtfobins                                                                                   
| Q11  | root.txt?                      			      | Nothing                                                                                    
