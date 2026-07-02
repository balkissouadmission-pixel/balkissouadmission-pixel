# Linux Commands Cheatsheet

## Navigation
- pwd — shows your current location
- ls — lists files and folders
- ls -la — lists everything including hidden files
- cd foldername — moves into a folder
- cd .. — goes back one folder up
- cd ~ — goes straight to home folder

## File Management
- mkdir foldername — creates a new folder
- touch filename — creates a new empty file
- cat filename — reads a file
- rm filename — deletes a file

## Searching
- grep "word" filename — searches inside a file
- grep -r "word" folder — searches all files in folder

## Permissions
- chmod 755 filename — changes file permissions
- whoami — shows current logged in user

## Log Analysis (grep)
- grep "word" /path/to/file — searches for a word in a file
- grep -i "word" /path/to/file — case insensitive search
- grep -n "word" /path/to/file — shows line numbers of matches
- grep "error" /var/log/Xorg.0.log — finds errors in display log
- grep "root" /etc/passwd — checks user accounts on system

## Monitoring Logs (tail)
- tail /path/to/file — shows last 10 lines of a log file
- tail -n 20 /path/to/file — shows last 20 lines
- tail -f /path/to/file — watches log file live in real time
- sudo tail /var/log/boot.log — reads protected log files as admin

## File Permissions (chmod)
- ls -la filename — shows current file permissions
- chmod 755 filename — owner can read/write/execute, others can read/execute
- chmod 644 filename — owner can read/write, others can only read
- chmod 777 filename — everyone can read/write/execute (use carefully)

## Permission Numbers Explained
- 7 = read + write + execute
- 6 = read + write
- 5 = read + execute
- 4 = read only