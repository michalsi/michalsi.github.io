---
layout: post
title:  "Linux - working with files remotley"
date:   2016-04-15 21:09:24 +0100
categories: linux
tags: scp ssh
---

We can use `scp` command to directly coppy files from remote server. Assuming that you have  ssh config set up on your local machine.

To copy file from remote server to your local directory:

	scp [remote host]:/path/to/file/on/the/server/log.txt .
	
## Log into remote server and execute commands

### Running a Command on a Remote Linux Server

	ssh [user]@[server] '[command]'
	
### Running Multiply Commands over SSH

#### with semicollons as separators

	ssh [user]@[server] '[command 1]; [command 2]; [command 3]'
	
#### with pipes as separators 

	ssh [user]@[server] '[command 1] | [command 2] | [command 3]'


#### with redirection of multiple lines 
	
	ssh [user]@[server] << EOF
	command 1
	command 2
	command 3
	EOF

to get it working I had to add `ssh -t -t` to force pseudo-tty allocation even if stdin isn't a terminal.

and `<< 'EOF'` to prevent the body of the here document being expanded by the (local) shell

```bash
ssh -t -t [server] << 'EOF'
	ls
	exit
EOF
```
