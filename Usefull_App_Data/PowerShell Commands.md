POWERSHELL USEFULL COMMANDS 
###To enter powershell from LINUX after SSH: "powershell"###
====
type >> same as "cat" in Linux >> reads the file and gives output >> type test.txt
			===
Get-Content >> read the contents of a file >> Get-Content -Path file.txt
			>> Get-Content -Path file.txt | Measure-Object -Word
				(the number of words contained within a file)
			>> (Get-Content C:\Windows\1.txt)[551]
				(THAT IS THE SYNTAX!!! exact position of a string within the file)
			===
Set-Location >> same as "cd" in Linux >> Directory hop >> Set-Location C:\Users\
dir >> lists files 
	>> -Force >> get hidden (.\) files
	===
Get-ChildItem >> same as "dir" above
				-Path				>> Specifies a path to one or more locations
				-File / -Directory	>> To get a list of files/directories
				-Filter				>> Specifies a filter for the Path parameter
				-Recurse			>> Gets the items thereand in "child" locations.
				-Hidden				>> To get only hidden items
				-ErrorAction SilentlyContinue >> After error action
		EXAMPLE: Get-ChildItem -File -Hidden -ErrorAction SilentlyContinue
				(view all of the hidden files in the current directory)
	===
Select-String >> search a file for a pattern  
			  >> example: Select-String -Path 'c:\users\ -Pattern '*.pdf'

Get-Help >> Help page >> example: Get-Help Select-String
Get-FileHash >> Get a files hash >> Get-FileHash -Algorithm MD5 file.txt






