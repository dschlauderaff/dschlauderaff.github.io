---
layout: post
title:  "Classes, #initialize, and understanding what you are looking for in REPL"
date:   2017-04-24 02:58:18 +0000
---


Started OO Ruby today. On the first real lab, school domain, I had a hell of a time getting the students to add to the roster hash. I was using pry in #add_student, and was having a hard time understanding why the class variable, @roster was not returning an empty hash.

For whatever reason, the first time I ran the #initialize method with @roster = {}, I got an error. So I pulled it out into it's own method
`def roster
    @roster = {}
 end`
 
 And the empty roster test passed. The problem that is so clear now, but I couldn't figure out for more than 30 minutes, was that now the roster wouldn't be initialized when School.new was created. Compounding the error, I couldn't figure out why @roster would be nil in REPL, but when I called (what I mistakenly thought) was the local variable, roster, that would return an empty hash.
 
 In hindsight, I can see that what I was actually doing in pry was calling the #roster method I had left out of #initialize, which then created the empty hash, and allowed the rest of my code to work properly using the class variable @roster. Once I realized what I had done, I moved the @roster = {} back to #initialize (without an error this time, I don't know what went wrong the first time to make me think I had to move it out), and magicallly all of my code worked. Frustrating evening, but a good lesson in understanding variable scope, initialization, and understanding what you are doing in a REPL.
