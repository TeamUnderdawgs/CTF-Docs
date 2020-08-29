## Owner:

> CaptainFreak

#### Solved?

 - Yes

#### Solutions:

As bodyParser.urlencoded has its option of extended turned on.
We can pass Arrays/JSobjects in post body.

So if content = ["Something"]

JSON.Stringify(content) = '["Something"]'

JSON.Stringify(content).slice(1,-1) wil be "Something" 

Hence we can break out of note string in frontend with post body 

content[]=;JSCode;

which will be content = [";JSCode;"]

and will get embedded in frontend as 

<script type="text/javascript">
	var note="";JSCode;""
</script>

We get XSS and we send payload of stealing TJmikes cookie with this.


#### More detailed Writeup:

https://7riple7hrea7.com/google/ctf/Pasteurize-GoogleCTF/