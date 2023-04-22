# HTB TEMPLATED WRITE-UP…..

![image](https://user-images.githubusercontent.com/44930131/233737037-6cf1288d-dd36-4f85-9215-c2c27850173c.png)

#### Dear Readers, I recently completed a HackTheBox web challenge called Templated..., a really short and fun Server Side Template Injection (SSTI) challenge was created. Let’s get right to the writing....

#### OUT LOOK OF THE WEBSITE

The website is fairly empty, with only some text and no clickable buttons, logos, or other elements.

![image](https://user-images.githubusercontent.com/44930131/233737181-2b85126f-83b3-4cff-9192-71ca0c0c4302.png)

I attempted to inspect the source code by inspecting elements, but there was nothing interesting in the HTML source code that gave any hints. The website did not provide any cookies. As a result, I decided to examine the HTTP response header in Firefox’s Network Tab and discovered something interesting. Werkzeug is used on the website.


![image](https://user-images.githubusercontent.com/44930131/233737211-79b613e9-cc41-4f38-a21c-8667143536f8.png)

If we search for Werkzeug’s common Remote Code Execution (RCE) method, we find that it is dependent on the debugger provided by Werkzeug. However, when I manually entered information, it did not save the console or the default location. It should not, however, be a dirbuster challenge.

![image](https://user-images.githubusercontent.com/44930131/233737273-9b151a7e-1844-463e-9b6e-0fb8f47d3666.png)

However, the website returns the exact text of the page or folder we searched. This relates to a common vulnerability on Jinja2 known as Server-Side Template Injection (SSTI), which corresponds to the hint shown on the first page as well as the challenge’s name, Templated. More information can be found in this blog post, which is very informative. I tried another random page and it displayed exactly what I entered.

As a result, ![image](https://user-images.githubusercontent.com/44930131/233737357-4942c966-8c5e-4881-a534-4dc69c8828f2.png)

#### SERVER-SIDE TEMPLATE INJECTION

When an attacker is able to inject a malicious payload into a template that is then executed server-side, this is referred to as server-side template injection.


```
http://vulnerable-website.com/?name={{bad-stuff-here}}
```

Because we know the server is running Flask, a Python library, we can use Method Resolution Order (MRO) to traverse up the request library in Flask to import the os library, as demonstrated in the blog post I mentioned earlier. Once we have access to the os library, we have shell access to the server and can run any command that will be printed on the template and rendered to us.

```
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
```

#### EXPLOIT-TESTING….

Experimenting with the exploit to see if we can access the os library and execute the id command.


![image](https://user-images.githubusercontent.com/44930131/233737651-30e4a286-a50b-40e4-8bd0-5215be91ef41.png)

We can now see the directory we are in and see if the flag’s file is there because we can run any command. The ls command returns the file flag.txt.

![image](https://user-images.githubusercontent.com/44930131/233737699-3aa5cd7a-4b46-42f9-aaca-43909e7be50a.png)

#### OBTAINED FLAG

Finally, we can print the flag contents.

![image](https://user-images.githubusercontent.com/44930131/233737727-5854ab2c-6cbb-419c-9171-98d48b2b42af.png)

The challenge is quite straightforward…………….
I hope you found this post useful. Please leave any comments below. You may also send me some suggestions.


















