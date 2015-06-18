# How-to build new version #
<font color='red'><h2> sketch </h2> </font>
## Compile from source ##
With [Debian](http://code.google.com/p/anmap/wiki/developers_land_debian2android) and [ConnectBot](http://code.google.com/p/connectbot/) or adb shell right on your device:
```
$ su
# bootdebian

# cd /usr/src
# wget http://nmap.org/dist/nmap-5.59BETA1.tar.bz2
# bzip2 -cd nmap-5.59BETA1.tar.bz2 | tar xvf -
# cd nmap-5.59BETA1
# ./configure --enable-static --without-nping --without-zenmap --without-ndiff --without-liblua  --without-ncat 
# make static
```
Or use this Makefile [nmap.android](http://anmap.googlecode.com/svn/branches/nmap.android)
```
$ su
# bootdebian

# cd /usr/src
# wget http://nmap.org/dist/nmap-5.59BETA1.tar.bz2
# wget http://anmap.googlecode.com/svn/branches/nmap.android 
# bash nmap.android
```

Now, you have compiled nmap, try to strip it:
```
# cp nmap nmap.not_stripped 
# ls -la nmap
# strip nmap
# ls -la nmap
```

And try to scan:
```
# ./nmap localhost
```


## Android GUI Wrapper ##
related:<br>

#<a href='http://code.google.com/p/anmap/'>code.google.com</a>
#<a href='http://code.google.com/p/androidterm/'>androidterm</a>
#<a href='http://code.google.com/p/cidrcalculator/source/browse/trunk/src/us/lindanrandy/cidrcalculator/Converter.java'>cidrcalculator/..blabla../Converter.java</a>
#<a href='https://github.com/rorist/android-network-discovery'>android-network-discovery</a>
#<a href='http://code.google.com/p/connectbot/'>connectbot</a>

<h1>Details</h1>
<h3>1.Daemon jobs class: </h3>
<i>SIGNALL's,<br>
kill<br>
-HUP<br>
-9<br>
etc</i>

Universal class to start/stop/restart/reload config/etc for any daemons from sshd to apache.<br>
<br>
<h3>2.Content(main.xml): </h3>
<pre><code>&lt;?xml version="1.0" encoding="utf-8"?&gt;<br>
&lt;RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"<br>
    android:layout_width="fill_parent"<br>
    android:layout_height="fill_parent"&gt;<br>
    &lt;TextView<br>
        android:id="@+id/label"<br>
        android:layout_width="fill_parent"<br>
        android:layout_height="wrap_content"<br>
        android:text="Enter target:"/&gt;<br>
   &lt;Button<br>
        android:id="@+id/button"<br>
        android:layout_width="wrap_content"<br>
        android:layout_height="wrap_content"<br>
        android:layout_alignParentLeft="true"<br>
        android:layout_below="@id/label"<br>
        android:text="nmap" /&gt;<br>
    &lt;EditText<br>
        android:id="@+id/entry"<br>
        android:layout_width="fill_parent"<br>
        android:layout_height="wrap_content"<br>
        android:background="@android:drawable/editbox_background"<br>
        android:layout_toRightOf="@id/button"<br>
        android:layout_below="@id/label"/&gt;<br>
&lt;/RelativeLayout&gt;<br>
</code></pre>

<h3>3.Edit: nmap flags & target </h3>
<br>
big christmass tree button, you push it and look at tcp packets with rather flags fly from left to right side of you mobile device monitor.<br>
from right to left...<br>
etc.. <br>

<h3>4.Button: start nmap</h3>
<pre><code>final Button button = (Button) findViewById(R.id.button);<br>
button.setOnClickListener(new View.OnClickListener() {<br>
	public void onClick(View v) {<br>
	Toast.makeText(v.getContext(), "Start scanning...\n" + cmd , Toast.LENGTH_SHORT).show();<br>
	// Scanning!<br>
	Toast.makeText(v.getContext(), "Scanning finished!\n", Toast.LENGTH_SHORT).show();<br>
	}<br>
});<br>
</code></pre>

<h3>5.Get output(like connectbot)</h3>

<a href='http://code.google.com/p/connectbot/'>connectbot</a>

<br>
<h3>6.Get root right</h3>
<a href='http://www.google.com/search?btnG=1&pws=0&q=request+root+access+android'>http://www.google.com/search?btnG=1&amp;pws=0&amp;q=request+root+access+android</a>

<a href='http://www.stealthcopter.com/blog/2010/01/android-requesting-root-access-in-your-app/'>http://www.stealthcopter.com/blog/2010/01/android-requesting-root-access-in-your-app/</a>

<pre><code><br>
public static String runRootCommand(String cmd) {<br>
       String output = "";<br>
       Process process = null;<br>
       DataOutputStream os = null;<br>
       	try {<br>
       		process = Runtime.getRuntime().exec("su");<br>
            os = new DataOutputStream(process.getOutputStream());<br>
            os.writeBytes(cmd + "\n");<br>
            os.writeBytes("exit\n");<br>
            os.flush();<br>
            process.waitFor();<br>
        } catch (Exception e) {<br>
        	String msg = "Unexpected error: " + e.getMessage();<br>
            Log.d("*** DEBUG ***", msg);<br>
            return msg;<br>
        }<br>
       finally {<br>
            try {<br>
            	if (os != null) { os.close(); }<br>
                process.destroy();<br>
            } catch (Exception e) {<br>
            	// nothing<br>
            	}<br>
       }<br>
       return output;<br>
   }<br>
<br>
</code></pre>

<h3>7.Button(Menu): Help </h3>
<pre><code>Man page / ( nmap -h ) / some doc or links<br>
</code></pre>
<h3>8.Button(Menu): Bug report/Test how it doing</h3><br>
<i> (Verbose, Sillent)</i> <br>
<font color='green'><h2>Button(internal): Send bug report.</h2></font><br>
try to send me email - bug report with full debug log  of root/unroot scanning, and other info, such as: <br>
1. all system data: set, env, root_dir, sdcard_dir, file systems, phone generation, os version etc. <br>
2. md5,sha256 of current unpacked apk release.<br>
3. result of scanning some special targets.(some tower wich will log it) + timestamp<br>
<br>
<blockquote><br></blockquote>


<h3>9.Button(Menu): Journal</h3>

<i> history by ips/domain-names located on sdcard. </i>
When you try again any ip, you can get diff highlighted on old scan log  "+"/"-" new/old line.<br>
Some cool format to contain this diff in long time. <br>
with all things <br>
<br>

<h3>10.Monitor mode(like <a href='http://en.wikipedia.org/wiki/Iftop'>iftop</a>, <a href='http://freshmeat.net/projects/nload/'>nload</a>, <a href='http://nethogs.sourceforge.net/'>nethogs</a>, <a href='http://www.phildev.net/iptstate/'>ipstate</a>, trafshow) (probably, stand alone app)</h3>
<pre><code>Network discovery.<br>
<br>
Check every ip/arp connection(OS, bytecount in/out, proto).<br>
<br>
View in real time state netland, like a top(UNIX).<br>
<br>
(by getting headers wireshark/tcpdump)<br>
<br>
(by current network settings)<br>
<br>
(by nmap -O -iL current_ip_list)<br>
<br>
(by mac address, etc, create some profile and monitoring this machine that you have to contact)<br>
<br>
</code></pre>