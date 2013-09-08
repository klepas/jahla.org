---
layout: post
title:  "Applescripts for iTunes via Alfred App"
date:   2013-09-09 13:31:06
categories:
---
Sometimes iTunes can be frustrating, especially if you have large amounts of music to sort through and possibly trash. Personally, I like to do this while working on something else, so I don't want to have to go through the whole process of sorting and deleting manually.

Like (I assume) many DJs and music lovers, I have a lot of music. But I don't want to have to stop, go to iTunes, skip through the offending song to check it doesn't get better, ctrl+click Show in Finder, cmd+del, cmd-tab iTunes, ctrl+click Show in Playlist> Music, delete from iTunes library…

Wouldn't it be great if there was a way to do all this from a hotkey from any application? A way that didn't require any other input, so you didn't have to pause what you were doing elsewhere? 

As it turns out, one way of doing this is by setting global hotkeys in [Alfred.app](http://www.alfredapp.com/) which trigger applescripts to deal with iTunes. Note that these actions are done using applescripts, but if you don't want to have to put ugly shortcuts in your global scripts menu or switch to another app to run the scripts, you need some way of setting a global hotkey. This is where alfred.app comes in, although you need the paid powerpack version to create custom workflows and hotkeys. 


<dl>
<dt>Kill current track</dt>
<dd>Deletes current iTunes track from iTunes, file to trash. Plays next. *Use carefully, no confirmation dialogue*</dd>
{% highlight applescript %}
global addenda
tell application "iTunes"
	if player state is not stopped then
		set ofi to fixed indexing
		set fixed indexing to true
		try
			set dbid to database ID of current track
			set cla to class of current track
			try
				set floc to (get location of current track)
			end try
			try
				delete (some track of library playlist 1 whose database ID is dbid)
			end try
			set addenda to "Done. The track has been deleted."
			if cla is file track then
				set my addenda to "Done. The track has been deleted and its file has been moved to the Trash."
				my delete_the_file(floc)
			end if
		on error
			set addenda to "The track could not be deleted."
		end try
		set fixed indexing to ofi
	end if
	next track
	play
end tell
to delete_the_file(floc)
	try
		-- tell application "Finder" to delete floc
		do shell script "mv " & quoted form of POSIX path of (floc as string) & " " & quoted form of POSIX path of (path to trash as string)
	on error
		set addenda to "Done. However, the file could not be moved to the Trash."
	end try
end delete_the_file
{% endhighlight %}
<dt>Playback position</dt>
<dd>Applescript to skip forwards and backwards in currently playing iTunes track. In this example, the script tells iTunes to skip back 20 seconds</dd>
{% highlight applescript %}
tell application "iTunes"
	if player state is stopped then return
	try
		set player position to (get player position) + (-20)
	end try
end tell
{% endhighlight %}