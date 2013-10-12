---
layout: post
title:  "Applescripts for iTunes via Alfred App"
date:   2013-09-09 13:31:06
categories:
---

_Sometimes iTunes can be frustrating, especially if you have large amounts of music to sort through and possibly trash. This post outlines one way of creating hotkeys to help streamline this process._

You are in the middle of something, headphones on — when the music changes. Perhaps its an alphabetically adjacent album, or you were using shuffle, or this track has the most irritating breakdown you have ever heard. Your hard won, jealously defended moment of serene concentration is broken. 

I have collected a lot of music over the years, much of which I have forgotten or moved on from. I suspect the same is true for many other music lovers, DJs and garden variety data whores out there. But I don't let iTunes organise my files for me, and so sorting and deleting manually can get arduous. I don't want to have to stop, go to iTunes, skip through the offending song to check it doesn't get better, ctrl+click Show in Finder, cmd+del, cmd-tab iTunes, ctrl+click Show in Playlist> Music, delete from iTunes library…

_A global hotkey that does all this, with minimal disruption to workflow._

One way to do this — there are many, and I don't claim this is the best way but merely that it works for me — is to use the PowerPack version of [Alfred.app](http://www.alfredapp.com/) to create hotkey triggers for iTunes applescripts.

The scripts listed here are variations on some of my favourites from Doug of [Doug's Applescripts for iTunes](http://dougscripts.com/itunes/). 

I didn't want to have to put ugly shortcuts in my global scripts menu or switch to another app to run the scripts, so I needed some way of setting a global hotkey. This is where alfred.app comes in, although you need the paid powerpack version to create custom workflows and hotkeys. 

<dl>
<dt>Kill current track</dt>
<dd>Deletes current iTunes track from iTunes, file to trash. Plays next. *Use carefully, no confirmation dialogue*</dd>
<dd>**Important edit** As of iTunes 11.1, this script no longer starts playing the next track in the playlist but takes you back to the first one. I will post the fixed version asap. 
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