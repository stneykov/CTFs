# The weather outside is frightful… (stegano) (author: stn)

## Description

[flag.txt](flag.txt)

## Task analysis & solution

The only piece of information provided is a file called flag.txt in this challenge. Upon opening it, we are greeted by some familiar lyrics:

```
Oh, the weather outside is frightful	     	 	       	    	     
But the fire is so delightful       	   		    	    	      
And since we've no place to go	      	 	     	 	      	     
Let it snow, let it snow, let it snow      	 	  	 	     
It doesn't show signs of stopping  	      	   		    	      
And I've bought some corn for popping   	  	       	   	    
The lights are turned way down low      	    	 	    
Let it snow, let it snow, let it snow	   	       	   	 
When we finally kiss goodnight	      	      	      	   	       	   
How you'll hate going out in the storm  	     	       	      	       
But if you really hold me tight      	       		     	   	    
All the way home you'll be warm    	   	      	 	     	       
The fire is slowly dying     	     	     		       	  	    
And, dear, we're still goodbying 	    	    	     	   	  
But as long as you love me so 		 	       	      	     
Let it snow, let it snow, let it snow
But if you really hold me tight
All the way home you'll be warm
The fire is slowly dying
And, dear, we're still goodbying
But as long as you love me so
Let it snow, let it snow
```

What we're also greeted by, are some trailing symbols for what appeared to be whitespace characters when highlighting the text...

![Image preview](1.png)

I had to load up the file in a more sofisticated text editor than Notepad to see all characters. Namely, Notepad++:

![Image preview](2.png)

Those appear to be tabs and spaces, but what exactly is it? Some sort of encoding? I looked online and there seems to be a particular tool that is used to conceal data inside text files with tabs and spaces: [stegsnow](https://manpages.ubuntu.com/manpages/bionic/man1/stegsnow.1.html). Let's give it a try and see if it will decrypt our text :)

```sh
$ stegsnow -C flag.txt
MilestoneCTF{we-wish-you-all-white-christmas}
```

And there we have it, the flag was **``MilestoneCTF{we-wish-you-all-white-christmas}``**