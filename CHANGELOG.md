﻿Version History
===============

v3.3.4 - Oct 2023

Removed default XML file name specification that is used for storing some last used settings for greater cross platform suitability, so it just uses a system default instead
Corrected project version specification for Windows (as it was left at v3.3.1 in error)
Adjusted the "start at a time" date to a more current default value to save users having to scroll from 2017!
Removed dependancy on the no longer maintained ZVDateTimePicker in favour of the native TDateTimePicker, again for easier cross platform compilation
Apple OSX version released again that should now work on newer Mac OSX versions, in theory


v3.3.3 - Oct 2023
By popular request, the last selected hash algorithm will now be saved on exit and re-used on next launch. Settings saved in QHConfig.xml file.  
The correction and conversion from "ID" to "No" was not fully integrated in the "Compare Two Folders" tab, causing an SQLite error message to appear. That was fixed. 
Fixed countless deprecated references to "ID" column in SQL syntax - another remnant of previous changes from "ID" to "No".
Removed some unnecessary logging data from the log file of "Compare Two Folders" tab, notably entries that just mirrored the UI during the process such as "Currently searching for...".
SQLite version 3.43.2 bundled in 32 and 64 bit modes. 
This changelog corrected to show the release date of v3.3.2 as June 2023, instead of Jan 2022! *
About page updated some more. 
 
v3.3.2 June 2023  *
The column heading of "ID" in text output seems to cause the almighty Microsoft Excel a headache because it thinks it is an "SYLK" file. And users of Quickhash were being told "Excel has detected ‘file.csv’ is a SYLK file, but cannot load it. Either the file has errors or it is not a SYLK file format. Click OK to try to open the file in a different format." I was unaware a two byte string of "ID" at the starts of a CSV file somehow meant "all such files are SYLK files and cannot be anything else" but there we are. So yes, I have changed the value of "ID" to "No" (as in "Number") for now. 
Users of v3.3.1 can do the following if they do not want to upgrade to v3.3.2 : 
  Open the CSV with a text editor like Notepad and change ID into some text that doesn’t start with ID e.g No or Number
  Save the CSV then open it with Excel. This time it won’t throw you the SYLK file error. Do your editing and save the CSV.
  Open the CSV with the text editor again and change the edited text back to ID. Save the file. It will now be OK. 

In the Copy tab, if the user had checked the box "Save results (CSV)?", they were getting a largely empty output file. But if they right clicked the display grid of results and saved to CSV, it was OK. That was fixed. It was another error remaining of my v3.3.0 fixes back in May 2021 and I didnt catch it in the v3.3.1 release. Sorry for that

In the Copy tab, the little text based percentage indicator (as controlled by lblFilesCopiedPercentage object) had somehow got buried in blankets like a small child at a crazy feather pillow-fight party at some stage in development history and was not visible. Now it is. 


v3.3.1 6th Jan 2022

function CountGridRows has been changed. This function was designed to count the number of rows in any given display grid to determine whether the clipboard could be used, or whether the data would saved to a filestream. And, if the user chose to save the output to CSV or HTML, the same function would check to see if a memory list of strings could be used to then be saved out to a file, or whether a filestream should be used line by line. 

But, when saving the output of very large lists of files to HTML, filestreams were supposed to be incorporated rather than using RAM. However, due to the v3.3.0 adjustment of the function CountGridRows to use .RecordCount, .First and .Last, the variable that was used to check the number of rows was only showing what was on screen instead of what was in the table. So, QH was still using RAM even if the row count was many hundreds of thousands!! As such, it would cause QH to crash with large volumes of data. Fixing this required tow significant changes: 

1) Changes to CountGridRows means a dedicated TSQLQuery is used on the fly, instead of the DBGrid itself. 
2) Changes to the function call of CountGridRows now means the Grid and the table to query is passed. 
3) Major changes to functions SaveFILESTabToHTML SaveCOPYWindowToHTML SaveC2FWindowToHTML to use TSQLQueries too, instead of DBGrid queries. All three can now handle many thousands of rows more easily and are executed in just a few seconds. A test of 407K rows was saved as a 56Mb HTML file in under 10 seconds. However, I have noticed that the step of preparing the data for display in the Compare Two Folders does take a long time for many tens of thousands of files. It gets there eventually, but it can take a while. This is due to the enormous SQL statement that was added in v3.3.0 in the repareData_COMPARE_TWO_FOLDERS function. This was added to give users greater abilities to find and sort, following earlier pre v3.3.0 complaints that the compairson was not granular enough. It is more granular now, but has come at the cost of taking longer to prepare. Something to work on for v3.3.2.  

These changes described above are the largest service release aspects to this version. 

The user is now also shown a message on screen, with an OK button, to let them know a Save as HTML has finished. Useful if the data set is very large and the save takes some time.  

The HTML file produced by right clicking in the FileS tab did not have a row 1 header if the row count was over 20K. Now it does. 

The HTML file produced by right clicking in the FileS tab did not have the FileSize column if the row count was over 20K. Now it does. 

The HTML file produced by right clicking in the FileS tab did not have the ID column if the row count was LESS than 20K. Now it does. (note that this has not been added for clipboard output on the assumption it would be pasted into spreadsheets where rows are automatically then counted)

(See - there were a lot of things missing in the HTML save for large volumes of data that I had missed - this is how small scale testing on your own does not compare with real world usage - its only when users report issues to me that I often get to know about problems, and then in turn, that unearths other issues that I can then fix)

On Linux, and OSX, the "Curently Hashing" status in the FileS tab was chopping off the first characters of the path. So instead of it saying /home/user/Documents/MyFile.doc it was saying e/users/Documents/MyFile.doc. This was due to the long path override character cleansing that is necessary for Windows, but not for Linux or OSX, and I forgot to use a cross compiler directive. Now fixed in in v3.3.1

The function DatasetToClipBoardFILES checked if the number of rows was less than 20K, but didn't show a message to instruct the user to use a file save if the count was greater than 20K. That has now been applied in v3.3.1, so they dont just sit there wondering what has happened. 

If the user tried to clipboard a volume of data over 20K rows in the FileS tab, although the user was told to use a file save instead, the status still said it was copying to clipboard. Now it will tell the user the clipboard effort has been aborted. 

The Clipboard button in the "Copy" display grid was not as complete as the right-click clipboard option. A remnant of the changes made in v3.3.0. I think. Now both methods produce the same clipboard content. 


 


v3.3.0 May 2021<br>
New : Ability to hash forensic images of the Expert Witness Format (EWF), also known as "E01 Images". Available for Windows and Linux for users who know what they are doing with regard to forensic images. It is not available for OSX, for now. Quickhash will conduct the hash and also report the embedded MD5 or SHA1 hash, if available, placing it in the "Expected Hash" field automatically, depending on the type of hash the user is performing. So if the E01 contains both MD5 and SHA1, but the user selects SHA1, then the embedded SHA1 hash will be reported as well as the computed SHA1 hash, and the same theory for MD5. More features will likely follow in future of this landmark addition to QuickHash GUI. <br> 
New : CRC32 algorithm added for Text, File, FileS, Compare Two Files, Compare Two Folders and Base64. Not added to disks, and not available for EWF (E01) image hashing. 
New : Users who utilise the CRC32 algorithm via the FileS tab can now optionally choose whether to just compute the checksums of the files in the folder as normal, or, compute the checksums and then rename the files by appending the checksum in square brackets to the end. Useful for many media and sound specilists who commonly use CRC32 values in their work.  
New : Button added to enable the user to easily make a copy of the backend SQLite database at any given point in time, for convenience. This can help users who may wish to load it into specific database tools, like SQLite Explorer or browser extensions like SQLite Manager. <br>
New : The About menu now contains a "Check Environment" option (available on all OS platforms but the results vary on each) that scans for DLLs, reports database information etc. 
New : Logo replaced with the newer Quickash logo. <br>
New : In some parts of QH where display grids of data are generated (FileS, Copy, Compare Two Folders), the user can now select their own delimiter character via a drop down menu, such as the tab character, hyphen and (heaven forbid) even the space char. If no character is chosen, a comma is assumed and used as before. <br>
New : The user can now use the About menu to establish the version of SQLite that is being used by QuickHash. <br>
Improvement : Monumentally large changes to "Compare Two Folders" processing, scrubbing away much of the earlier effort and restructuring it, with big thanks to an open-source co-developer who has helped me here. Key amongst them are that v3.3.0 addresses a bug where rows got mis-aligned if the file counts differed. The mis-match was still correctly reported in v3.2.0, and even if the file matches counted but the hashes differed, that was also still OK. But, the rows got out of sync if the file counts differed due to there being less files in one folder than the other and my use of the UPDATE SQL statement. Additional restructuring applied but note that C2F is really designed to check that two folders are a mirror image of each other, and it is supposed to help you confirm this is the case, rather than help you clean up your disk. If your aim is to use it as a file manager, then QH might not be the best option. Other tools like Beyond Compare might be better for your needs here. <br>
That said, the ability now exists to compare files by name and hash value in both folders, and then the user can right click the results and see many other options too : <br>
~  Restore Results view<br>
~  Clipboard all rows<br>
~  Clipboard selected row<br>
~  Clipboard all selected rows (currently does it in reverse order for some reason)<br>
~  Show mismatches (new, based on filename or hash or both) <br>
~  Show duplicates (new, offers the chance to clipboard immediately after because the column row changes for this display)<br>
~  Show matching hashes (new) <br>
~  Show different hashes, not missing files (new)<br>
~  Show missing FolderA files (new)<br>
~  Show missing FolderB files (new)<br>
~  Show missing files from Folder A or FolderB (new)<br>
~  Save as CSV file<br>
~  Save as HTML file<br>
That is a whopping array of ways to conduct some analysis of two folders and is about as good as I think I can make it and is based on help from the community. If that still falls short, other tools are available, or get stuck in yourself and help me. <br>
Improvement : DB Rows were being counted (when required) using a slower method than I had realised. With v3.3.0, counts are now immediate by calling DBGrid.DataSource.DataSet.RecordCount; <br> 
Improvement : Column headers added to CSV and HTML outputs (achieved by right clicking the display grid results throughout). I may have missed one but I think I have them all covered <br>
Improvement : Removed the generation of a "QH_XXXXX" time stamp named parent folder in destination folder when copying as many users reported this was unhelpful.<br>
Improvement : SQLite DLL's for Windows replaced with stable version 3.35.5.0 as of April 2021 (replacing former version 3.21.0.0).<br>
Improvement : The size of some fields in SQLite was set to 32767 to account for crazily large filename and filepath combinations. But on reflection, that seems extreme use of memory for what must be one in a billion chances and very unlikely to be encountered. Instead, 4096 size is set in v3.3.0 to still enable QH to account for very long paths, but given that filenames alone can rarely exceed 255 (even where paths can) on any of the 3 OSes except for some UTF8 and UTF16 variances, where even with those the maximum is still 1020 bytes (4 bytes for every single char of the 255 max)<br>
Improvement : Disk hashing module now presents more data in the list view, especially for logical volumes, such as the filesystem. <br>
Improvement : The button to launch the disk hashing module now gives the user an indication of what it is doing while it loads the treeview of disks and volumes<br>
Improvement : The system RAM label has been moved from the main interface to the new Environment Checker section of the About menu (Windows only). This frees up some GUI real-estate and avoids the use of resources unnecessarily. 
Fix : DisableControls and EnableControls used more extensively to expedite the "Save as CSV" and "Save as HTML" options for large volumes of data, as some user reported save efforts taking several hours for millions of rows of data. This makes sense because Quickhash was repaitning the display grid after each row file write.  <br>
Fix : When saving results as CSV in Compare Two Folders, if the user selected an existing file to overwrite, it would do that, but the next run would result in an infinite loop telling the user it already exists and to choose another file, but not being able to actually do so. That was fixed. <br>
Fix : Apples new OSX 'Big Sur' OS unhelpfully removed static libraries, like the SQLite library, so it could not be referenced by file path. So a different method of lookup is needed using the dyanmic linker cache and a 3 state compiler directive is now used for loading SQLite, depending on the OS being used. That has been applied so that Apple users can continue to enjoy the benefits of QuickHash on that most changing and challenging of operating system. You're welcome. <br>
Fix : Two stringlists are created when using "Compare Two Folders" to store the list of files for analysis. I had introduced a memory leak here without realising it and that has been corrected (with thanks to an open-source developer who spotted that for me). <br>
Fix : A small memory leak existed in frmSQLiteDBases.DatasetToClipBoard for copying data to clipboard. The CSVClipboardList string list that was used to achieve this was not being freed. Now it is freed. <br>
Fix : In the basic results txt file that is created during Compare Two Folders, the selected folder names in the log file were prefixed with the LongPathOverride of two slashes a question mark and a slash. That was corrected to just show the normal path as users dont realy need to see that (as it is just an API switch). <br>
Fix : .Value was used extensively to "call" a value from a DB cell. But some cells can be NULL in QuickHash. And if they are, using .Value can generate an error. Instead this is now switched to .AsString meaning a NULL value returns an empty string, as intended. <br>
Fix : In the Text tab, the "Expected Hash" lookup was not applied for xxHash, which was missed before so if users pasted an expected xxHash value, it would not be looked up against the computed hash. That was fixed. <br>
Fix : In the File tab, the "Expected Hash" lookup was not applied for xxHash, which was missed before so if users pasted an expected xxHash value, it would not be looked up against the computed hash. That was fixed. <br>
Fix : The disk hashing module showed the field for Blake after hashing, even if empty and not computed, and was not being hidden like the others. That was fixed.  <br>
Fix : The disk hashing module reported "Windows 8" when conducted using "Windows 10". This was not actually wrong, but mis-leading, and is actually due to the Windows API being woeful in parts with regard to how the "number" and "name" of Windows are reported. So a new function created to speak to ntldr.dll directly so that now the major, minor, and build versions are all reported. </br>
Code : Adjusted variable naming in the "ProcessDir" function relating to source and destination folders because it was so confusing I did not even understand it several years after first writing it. <br>
Code : More effort made to initialise variables <br>
Code : Dismodule code entirely refactored to be more efficient, to produce more useful data for the user, and to help safeguard against null values, removable drive bays with no disks, and for general ease of reading. It should now also read (be able to hash) CD and DVD disks, for example. <br>

v3.2.0<br>
New : Blake3 hash algorithm added for text strings, a file, Files recursively, Compare Two Folders and Compare Two Files. <br>
New : Blake3 hash algorithm added to disk hashing module<br>
Fix : Hashing of physical disks in Linux via the "Hash Disk" module is re-enabled after "Access Violations" reported for earlier versions. <br>
Fix : In the "Compare Two Folders" tab, if the "Log Results" was unticked, it generated an access violation. That was fixed. <br>
Fix : In the "Copy" tab, when the results are shown in the display grid, the navigation buttons were not clickable. That was fixed. <br>
Fix : In the "FileS" tab, when the results are shown in the display grid, the navigation buttons were not clickable either. That was fixed. <br>
Fix : "Time Taken" in the "File" tab was showing a 24hr clock instead of showing as the number of seconds elapsed, as intended. That was fixed by utilising GetTickCount. <br>
Fix : In the "Compare Two Files" tab, the "Result: Match" value was only showing if the timed scheduler was invoked (though the actual hashes were still being displayed). This was caused due to a loop error where the result only displayed inside the scheduler loop. This has been fixed by moving it out of the loop, so that the result is shown either immediately with no scheduler being used, or following the scheduled invoke. <br>
Update : "LCL Scaling" is now incorporated which will hopefully better enable the GUI display on variously sized resolution settings. User feedback will confirm in due course. <br>
New : In the 'Compare Two Folders' tab, users have asked for a grid view of the files compared rather than just a text file output. That has been added with many of usual right click menu options such as copy to clipboard, save as HTML, etc.  <br>
Update : In the 'Compare Two Folders' tab, the option "Cont. if count differs?" has now been removed for several reasons:<br>
  1) Users were frustrated at analysing often millions of files only to realise after the event that in order to continue if the file count differed they had to do it all over again with the option checked<br>
  2) More users than I anticipated when I first added that feature use the "Compare Two Folders" comparison to not check that both folders do indeed match but in fact to determine in what way they differ. <br>
  3) It is more thorough, allbeit slower, to itteratively check the hashes of both folders both ways rather than checking based merely on count. <br>
Fix : In the 'Compare Two Folders' tab, if the user selects "Log Results" (which is enabled by default), then in Linux and OSX, the text log file was only being populated with the hash values and not the filenames too. That was fixed. <br>
Important Fix : In the 'Compare Two Folders' tab, the comparison was not sufficiently two way, meaning that if Folder 2 matched Folder 1 it would report a match, but if Folder 1 did not match Folder 2, it might still report a match when it should report a mismatch. THis has now been modified to a 3-way comparison. First it checks the file count, then is compares both hash lists against each other; HashListA against HashListB, then HashListB against HashListA. It has obviously made the comparison slightly slower for millions of files, but hopefully not too significantly, and accuracy is more critical than speed. <br>

Here is a copy of the bug report, provided for full transparancy : <br>

  "The folders were set up like this:<br>
  Folder 1: File A, File A (copy)<br>
  Folder 2: File A, File B<br>

  When running the compare feature, selecting Folder 1 and then Folder 2, the tool reported a match, "The files in both folders are the same. MATCH!" I had expected a mis-match. Yes, all of the files in Folder 1 are in Folder 2, but  not all of the files in Folder 2 are in Folder 1. If I re-ran the compare feature in reverse, selecting Folder 2 and then Folder 1, a mis-match was reported, "The files of both folders are NOT the same. The file count is the same, but file hashes differ. MIS-MATCH!" This seemed odd because if I ran this features with the following situation:<br>
  Folder 1: File A<br>
  Folder 2: File A, File B<br>
  I would receive a mis-match message, but the situation is basically the same, all Folder 1 files are within Folder 2, just like if two copies of File A were in Folder 1, which reported a match.<br>
  I had expected a back and forth comparison, but it appears to be a one-way comparison. "<br>

I thank the reportee who brought this to my attention and it should now be resolved in v3.2.0. <br>
 
v3.1.0 (July 2019)

Update : HashLib4Pascal library updated to master version available as of 18th July 2019.
New : Added SHA-3 (256) hash algorithm 
New : Added Blake2B (256) hash algorithm (best on 64-bit systems, faster than MD5, SHA-1, SHA-3, SHA256 and SHA512 and more reliable than MD5, SHA-1 and comparable to SHA-3) : https://blake2.net/ 
New : The FileS tab right-click menu now includes 'Copy all hashes' option, to clipboard ALL the hash values in the hash column. If > 10K values, will ask user if he wants to write to a file instead. 
Fix : The "Compare Two Files" tab had a bug. If the user clicked the resulting hash, it would be copied to clipboard correctly but be described as MD5 even if the chosen algorithm was not MD5. Fixed. 
Fix : In all tabs, xxHash in 64-bit mode did not show a progress bar. The 32-bit version did though. That discrepency was fixed. 
Fix : When comparing two folders, if there was a count mis-match, the log showed the same name for both folders, instead of "Folder A and Folder B", it said "Folder A and Folder A". Fixed. 
Fix : In the FileS tab, when exporting BILLIONS of files an out of memory limit was reached. This should now be fixed due to implementation of a file write stream instead of using CSVExport library.
Fix : Theoretical compliance to Apple OSX Catalina 64-bit enforcement. 
 
v3.0.5 (July 2019)

Not compiled version released as only minor code cleanup for the codebase, in advance of v3.1.0 development. 
Fix : Adjusted date and time formatting in the FileS tab to ensure default date and time settings used instead of UK or USA style of formatting. 

v3.0.4 (Jan 2019)

The 'File' tab was not showing automatically when using drag and drop. Now it does.

On Linux and OSX, the program will now automatically use default system wide SQLite libraries. This should reduce the risk of the program failing to load due to an SQLite file not being where it is expected to be.

Reverted the "hash matches expected hash" or "hash does not match expected hash" back to how it was before v.3.0.3. Users complained that it was more confusing in v3.0.3 than previous versions. So now it is back to the normal OK dialog and it just tells you if it matches or not.

v3.0.3 (Jan 2019)

The 'Load Hashlist' functionality in the 'FileS' tab would accept all values but then compare them against computer uppercase hashes strings, meaning any lowercase imported values were not matched. 
Now all input is converted to uppercase first. 

The 'Load Hashlist' functionality in the 'FileS' tab can now accept CSV saved files (but must still contain only one column of hash values!)

The 'Known Hash Flag' column, where it reports either YES or NO after importing a hash list, was only showing with the default display grid in FileS tab. If the user resorted, the column would vanish. Now it should remain for all sorting. 

The filehandles were not always being released properly, even if hashing completed OK, thus leaving open handles to processed files. A bug I introduced by mistake in v3.0.2. by trying to free handles more gracefully! That was fixed. Report issues with this if still not working properly. 

The 'FileS' tab was computing file hashes of zero bytes returning the default initialisation hash for the chosen algorithm. I thought I had corrected that a long time ago but I may only have done 
so for the 'File' tab according to the release notes. That was fixed returning instead 'ZERO BYTE FILE'. 

clear the hashes do, or do not, match for
single file hashing in the 'File' tab or when using 'drag and drop' feature. 

The exe version has been updated to the correct numbering for the version (forgot to update in the project file last time). 

The program will now show either "64-bit" or "32-bit" in the main title if its 64 or 32 bit accordingly. 

The 'About' dialog has been updated and also re-aligned for word-wrap, to try and make it look neater. 

The 'About' dialog was showing an incorrect link to the donations page! Corrected to https://paypal.me/quickhashgui 


v3.0.2 (Mar 2018)
Filenames and paths were being truncated at 128 characters in length in the display grid, despite still finding and hashing the files. It was merely a display problem, but quite a signfiicant display problem for logging purposes!
Better string handling in SQLite dbases unit to ensure strings are not truncated
TCSVExport resources were not being freed when saving data to CSV. Now they are freed. Still need to work on memory management there though and spend some more time to improve it. 
The file mask field in FileS tab caused the progress stats to be inaccurate, because they did not take account of the fitlered file total and instead would use the folder file count total. That was fixed. 
The file mask field in Copy tab caused the progress stats to be inaccurate as well. That was fixed. 
The "List JUST sub-directories" and "List JUST sub-directories and files" had been disabled during the development of v3.0.0 by mistake. That was also fixed and no outputs the result to a text file. 
The "Make UPPER" and "Make Lower" buttons in Text tab didnt work properly on OSX (as in the has was not auto-recomputed for the new cases text). That was fixed. 
The "Switch case" tick box was made wider to avoid truncation. 

v3.0.1 (Feb 2018)
The "Select File" in File tab generated an unnecessary error if the user cancelled the selection. Now it just cancels as expected
If QuickHash cannot get a handle to a file because it is open without share permissions by the OS, QuickHash should now silently proceed and simply report that the file could not be accessed in the hash column
The SQLite database is now located in the systems temporary directory and deleted afterwards rather than appearing in the root of the launch path.
In the FileS tab, if the user aborted a scan using Stop button and selected a new folder, nothing would happen because a boolean flag was not being reset properly. That was fixed. Date formatting also adjusted to YY/MM/DD (e.g. 18/01/31)
In the Disks tab, if the user had removable drive bays, often they would get error : "Could not convert variant of type (Null) into type Int64". That should now be fixed. 
In the disks tab, dates were being shown as dd/mm/yy. Changed to YY/MM/DD in line with the rest of QuickHash
In the disks tab, logical volumes were being shown with their API prefix (e.g. \\?\F:). It still will on initial selection but thereafter (once the hashing has started) it is converted to "F:"

v3.0.0 (Jan 2018)
Now with SQLite!! The reason why the development numbering has moved to v3.0...the first whole number release since v2.0 in 2013, is due to the move to SQLIte. This has been a massive re-write and a total overhaul of large parts of the program. SQLite adds many areas of functionality that was not possible before, so some tick box options have been removed in exchange for right click menu options. As a result of using SQLite, once the hashing has been conducted the user can still save and copy data to clipboard as he could before, but in addition Quickhash can now list duplicate files, match filenames, match file paths, and copy individual cells, and it will list the data in the blink of an eye. 

Added the often requested feature of hash lookup from existing hash set (arrived with Beta2 ofr v3.0.0). Available only in the FileS tab by way of a checkbox called 'Load hashlist' and a button to select the hash list file. So if the user has a list of existing hashes in a text file, he can import that into QuickHash and then compute hashes of files in a folder using the FileS tab. Any hashes that are in the hash list but are not computed of files in the selected folder will be output with 'Yes' or 'No' respectively in the display grid. The user can then sort and filter by those values. In tests, several hundred existing hashes are imported in less than one second using only a dozen or so additional Mb of RAM.

The "Compare Two Directories" tab has been entirely re-written (and renamed) to better determine if both directories match or not. Recent updates to this tab had got so confusing it was difficult to read and debug, and in v2.8.4, if the user selected to thos wall results instead of just errors, it went really slow again because I must have got something wrong in one of the loops. So I ditched it all and started from scratch. The coding is much enhanced now, more efficient, and with less (no?) memory leaks. It utilises TFPHashLists amongst other things to improve perfomance. And instead of having two display grids (which also slow things down and were not that useable in fact), the interface now has two treelists for the user to select FolderA and FolderB which are sorted alphabetically and then hashed upon selection as TFPHashlists and compared. 

Also renamed the "Compare Directories" tab to "Compare Two Folders" tab. It marries up better with the previous tab ("Compare Two Files") and it makes more sense to most modern computer users who have been raised on such terminologies. Fixed some cross platform issues with this tab too, but better enable it to perform across Windows, Linux and OSX.  

The "File" tab did not have a filesize check for zero byte files. So if the user happened to navigate to an existing but zero byte file, the user would get a "Division by zero" error. That was fixed. 

Files of zero bytes were skipped in the 'Copy' tab (recorded and counted, but not hashed) in earlier versions by design to avoid the default initialisation hashes returned when hash algorithms are pointed at zero byte data streams. However, some users report using QuickHash for backup and restore purposes, where zero byte files are often still necessary despite being empty. So v3.0.0 has adjusted how zero byte files are treated. They are now copied, and still recorded as a zero byte file but not hashed. Instead 'zero byte file' will appear where the hash value would.
The same fix has been applied to the "Compare Two Files" tab, where a zero byte file will not be hashed (because doing so just returns the default initialisation hash which is misleading). And also to the "Compare Two Folders" tab; zero byte files are now flagged as zero bytes. So still used for the comparison but not actually hashed. 

Some helpful interface hints added to the check boxes in the FileS tab,which seemed to have been left out of earlier versions

Following a feature request, added a "case switcher" to the Text and File tabs. This allows the user to toggle the output hash between upper or lower case. Useful for users who need to compute hashes of hashes, instead of just hashes of some data. For users who may need to compute the hash of a hash value, the case sensitivity is 
important obviously. Upper case is still the default output as it is easier for humans to read.   

Fixed the time scheduler. It now checks every 1/3 of second if the current time equals the scheduled start time.

Removed (deleted) an old unused progress bar that had been hidden by other elements

Text hashing SHA512 word wrapped the output so if it was copied and pasted into the expected hash value the carriage return went with it. So output field no longer word wraps. 

Ensured the GNU license delcaration and open source nature of the tool, as well as the copyright and home URL notice are in every unit file of the project to better protect it and to make the 
licensing clear. Added the GPL2 license, explicitally, to the Github project page. 

Linux specific : The disk hashing module was originally coded with udisk behind the scenes. That was deprecated long ago and is not available in many recent distributions of Ubuntu, Zorin OS etc as UDisks2 is now used, which
includes udisksctl. So the various technical data (size etc) is now queried and parsed with that instead, enabling users of modern Linux distributions to utilise the functionality. 

V2.8.4
------
The "Expected Hash Value" field had been broke a little in the 2.8.3 release meaning that when the user first pasted a value, it would report a mis-match even when it matched. But if the user re-pasted the value, it would match as intended (https://quickhash-gui.org/bugs/expected-hash-value-report-wrongly-on-single-file-hashing/). That fault was fixed. 
The "Expected Hash Value" was comparing only 7 characters instead of 8 for xxHash. That was fixed. 
The date and time formatting that was reported as fixed in v2.8.3 was not as fixed as it should be, and also was not included in the Linux version as it should have been.
The "Text" field had been accidentally adjusted to use pointers to widestrings. The commit was accepted without realisng the impact. So 'hello' was not being hashed but it's Unicode widestring version was being hashed. That was fixed and reverted to it's previous settings. 

v2.8.3
------

A new tab (the first new tab since disk hashing was added a couple of years ago!!) dedicated to Base64 decoding. The user can now select a file or a folder of files that are all Base64 encoded, and QuickHash will compute the hashes of both the encoded and decoded versions of the file, without the user having to supply a Base64 decoded version. In addition, as a little extra, there's also a third button in the new tab that the user can click to simply select a folder of Base64 encoded files and QuickHash will generate decoded versions of those files for the user in their chosen location with an appended filename. He can then go on to hash them if he so pleases, using the "File" or "FileS" tab of QuickHash. Once decoded and saved, the user has to establish what file types he has now and, for Windows users, this will mean adding the appropriate extension such as .pdf or .jpg to such files. Linux users will not have that problem because the operating system will work out what application is needed based on the file signature. The display grids also have options to copy the selected row, all rows, a range of rows, or save the display grid to a file. 

Added a date and time scheduler to most tabs (File tab, FileS tab, Copy tab, Compare Two Files tab, Compare Directories tab and Disks tab), meaning the user can set a date and time in the future to start the hashing process relating to these tabs. For example, say the user is copying a large file to a certain location that won't finish copying for a few hours; perhaps after the user has left the office or gone to bed it will finish, but he wants to have the file hashed for the next day. With QuickHash v2.8.3, the user can set a date & time in the future (to a level of hours and minutes precision, not seconds), to start a few hours ahead of time allowing the file to finish copying first. QuickHash will start hashing the file at that time. This functionality has resulted in the addition of the package 'DateTimePicker' (http://wiki.lazarus.freepascal.org/ZVDateTimeControls_Package) and so any developers will need to compile and then "Add to project" the package in Lazarus. (see section 1 of README.txt).

In the "Copy" tab, a grid display is shown on completion and that has been improved such that if the user right clicks it, he is shown the options to copy the selected row, all rows, a range of rows, or save the display grid to a TSV file. This is useful if the user unticks the "Save results to CSV" tick box, or if he accidentally clicks cancel when he is prompted to save that file (so when he has left "Save results to CSV" tick box ticked!). 

In the "Compare Directories" tab, if the user chooses to tabulate the results, upon completion of the comparison, if the user single left mouse clicks in either grid, the corresponding row entry in the other grid will be selected, assuming both directories actually match. If they do not match, the user will be taken to the corresponding grid row count, but this is unlikely to contain the same filename data of course. 

Progress bar added to "File" tab, for better progress feedback

"Expected hash value" field behaviour in "File" tab improved such that it only checks if the value pasted or typed into it is itself a valid hash value length for MD5 (32), SHA-1 (40) etc. And it is triggered not by a key press but by a changing value. And it will also apply if the user switches the hash algorithm to recompute a new hash as he may perhaps have chosen the wrong to start with.  

The "FileS" tab will now accept a root drive as a valid start location (e.g. C:\) allowing users to hash an entire logical drive volume, if they wish to do so (note that due to the fact that some files will be locked by the operating system, this approach is not recommended, save perhaps for USB drives and so on where it is less likely that the OS will have open handles to files). 

The source and destination path fields in the "Copy" tab are now read-only until the user (or if the user) clicks "UNC Mode", and then they become writeable. If the user deselects UNC Mode again, they become read-only again. This is to avoid users mistakenly typing UNC paths in them before enabling UNC Mode.  

Minor tweak to README.txt to properly refer to the HashLib4Pascal library

A line was added in 2.8.2 to free resources used by the check for duplicate files, but it was realised that this causes an error if a check for duplicates was not selected by the user, as there was no resource allocated to then free. That was fixed. 

Minor alignment fix to the labels at the top right of the "FileS" tab to better enable the values to be seen. 

The "Time Taken" value in "FileS" tab was showing incorrectly as "a time", for example, 12 seconds was being displayed as "12:00". Fixed using FormatDateTime and now shows as HH:MM:SS

Minor alignment adjustment to progress window at the bottom of "FileS" tab reduce chances of it being overlayed by the display grid. 

Some unused variables removed. 


v2.8.2
------

If the user clicks an alternative hash algorithm from the default (of currently SHA-1), the same selection will be applied to all the other tabs automatically. This is as a result of several requests to ensure that once the user chooses, say SHA256, that the selection remains as that selection throughout their session, even if they jump to other tabs. 

In the "copy" tab, there was a minor fault with multiple selection of source directories and some memory leaks due to unfreed lists. It should work better now, so the user can select FolderA and FolderB in source (as selected in the left pane) and have them both hashed, copied and re-hashed in a single new destination folder (as selected in right pane). 

Main menu added, comprising the ability to choose files and a folder (for now), and an About page to enable more information about the program to be visible. This includes information such as information about how to donate to the project financially, the homepage URL, author details, license info etc. 

Fixed minor memory leaks, trying to ensure resources are freed more robustly. 

Removed some more redundant code relating to units used when DCPCrypt was still part of the project, namely the assembly coded versions of MD5 and SHA1, which are no longer needed (which is a bit sad, as they were very impressive!). 


v2.8.1
------

All version of QuickHash prior (1.0 to v2.8.0) had problems accessing files that were opened by certain programs (not all programs) due to the way some programs share open files, and the way many refuse access to open files by other programs. For data hashing, ideally, open files should be closed. But in some cases this may not be possible and the inability to use QuickHash on such files was frustrating some users. So with this release, the file handle initiation has been altered to allow access to open files, on the understanding that the hash that the user gets may not be the same once that open file is closed later. 

The "File" tab now has the "ended at" date and time, instead of just the time, for instances where the end date may be the following day (e.g. started at 01/01/17 13:50 and ended at 02/01/17 00:10). Also neatened the alignment and ensured date formats were consistently presented as dd/mm/yyyy, instead of dd/mm/yy in one place and dd/mm/yyyy in another. Applied same presentation to drag and dropped files. 

The "File" tab now has a 'bytes read' countdown in the status bar of that tab, similar to the other tabs that have progress bars. This ensures that when a user is hashing a large file that takes more than a few seconds, he has some feedback as to what progress has been made, rather than the common belief that the program has just hanged. 
 
Fixed the -1 return count for files successfully copied in the 'Copy' tab, as per issue raised http://quickhash-gui.org/bugs/copy-tab-summary-form-show-1-file-count/ 

In the "Copy" tab, the currently selected path of the chosen "Source directory" and the currently selected path of the chosen "Destination Directory" will be visible by default now, unless the user ticks "UNC Mode?", in which case they switch over to allow direct UNC network path input. As per user request http://quickhash-gui.org/bugs/location-bar-for-copy-tab/ 

Some clearer hints added to the "Copy" tab and the percentage complete indicator aligned with status bar. 

The mouse-over hint in the 'Text' tab for the button 'TEXT Line-by-Line' was not showing. Now it does. 

In the "Compare Two Files" tab, the user can now use either the buttons to select their files, or they can paste the path directly into a path field. These fields are anchored to the right so maximising QuickHash makes them wider. Hint added to explain this. 

In the "Compare Two Files" tab, the user can single-click the resulting hash value and it will be copied to the clipboard, allowing the user to paste into whatever tool they wish (Notepad etc). The chosen hash algorithm will be prefixed to the value. Hint added to explain this. 

In the "Compare Files" tab, if the result is a match, it will say so in black (as it always has done) or it will be displayed in red if it is a mismatch, to ensure the user notices it more easily.  

Many redundant variables removed that have been left in over time that were no longer needed. 

Added a "Donate Now" label to the main interface, to try and encourage users to make small donations that can, in turn, help with the hosting costs of the website at AWS. 


v2.8.0 - Feb 2017
-----------------

Major change the the hash library. All version of QuickHash prior to and including v2.7.0 used DCPCrypt, which is a fairly old library and had to be adjusted to hash large files over 4Gb due to a 32-bit limitation. In addition, for SHA-256 and SHA-512, it was not enormously fast, though it was fast enough. With v2.8.0, HashLib4Pascal (http://wiki.freepascal.org/HashLib4Pascal and https://github.com/Xor-el/HashLib4Pascal) has been incorporated instead. There is not only a huge code readability improvement but a slight speed increase as well for all four of the major algorithms used by QuickHash. In addition, it will now make the addition of other other hash algorithms easier for the developers, because the library has a large choice to choose from. Enormous credit, appreciation and thanks to Ugochukwu Mmaduekwe Stanley, aka Xor-el, for the library (https://github.com/Xor-el) which is licensed under MIT. 

SHA256, SHA-1 & SHA256 concurrently and SHA512 hash algorithms added to the disk hashing module. 

xxHash64 added to all areas of QuickHash - text, files and disks. XxHash was a hash library that I wanted to include a couple of years ago but never got round to. But a Freepascal form of it is also part of the HashLib4Pascal library, so implementing it was as easy as for the other algorithms. It is true what they say about how fast it is - it really is crazy fast! 

New save dialog added to disk hashing module (prompted by default by the enabled "Created and save a log file" checkbox) to enable the user to save all the results of the hashing process as a text file in a location of their choosing. Or they can disable the option. 

New date and time values added to "File" tab so the user can report on the time the process started and ended and the elapsed time as per feature request http://quickhash-gui.org/bugs/add-date-and-document-output/ . Useful for benchmarking and so on. 

Also fixed the fact that the "Elapsed time" for the "File" tab did not refresh if the user changed the hash algorithm using the radio box. It only refreshed if the user chose a new file using the button. That was fixed so that regardless of how the user adds the file or what hash algorithm is chosen, the timers are reset. 

Horizontal scroll bar added to the hash value field in 'Text' tab, to allow the whole hash to be read more easily. 

Improved anchoring of several visual elements meaning text labels were not cut off or made less visible and looked better when maximising the GUI. Thanks to Dareal Shinji for his help with that. See https://github.com/tedsmith/quickhash/issues/11 

New Debian package added for experimentation - see https://github.com/tedsmith/quickhash/issues/2   

The settings file that was implemented in v2.7.0 caused some problems for Linux and OSX users. That was fixed by adjusting to a generic filename based on the name of the application. See https://github.com/tedsmith/quickhash/issues/6 

The progress bars didn't automatically reset to zero when the same tabbed interface was used multiple times without restarting QuickHash. Now, for each tab where a progress is found, when the user clicks "Start", or equivalent thereof, the progress bar will reset. 

Fixed an issue in the disk hashing module; after hashing a volume or disk, if the user selects a different hash algorithm and then clicks the start button again, 65K of data was read and hashed and then the program then just reports that no more data can be read. This was caused by the tripping of a boolean flag to true when the progress form was closed, thus, the repeat loop when executed again stopped at the "until" line because the abort condition was true. This was fixed. So now users can keep hashing the disk with various algorithms without restarting QuickHash.

New start date and time, end date and time and time taken labels added to the disk hashing module. This information is also saved to the log file by default. 

Stop button added to disk hashing module to allow the user to easily abort if needed. 


v2.7.0 - Dec 2016
-----------------

The "Compare Directories" now has a checkbox titled "Tabulate only encountered errors instead of all files (faster)?" to ask the user whether he wishes to tabulate only errors (hash mismatches or file count differences) rather than tabulating the entire folder selection of FolderA and FolderB. By not tabulating everything and instead only the few files that are different, lots of time is saved, making the program MUCH faster with large data volumes, and it is unnecessary to tabulate and log the comparisons of both folders if they are both the same anyway. If, in fact, the user wants a log of all the files and hashes of two given folders, he should use the "FileS" tab instead for this purpose (and as has always been the case). The save buttons are now disabled if no errors are detected, and enabled if there are errors. Unless the user unticks the "Tabulate only encountered errors instead of all files (faster)?" option, in which case everything is tabulated whether there are errors or not. Note, however, that with the option disabled, and if errors are encountered, there is likely to be two entries for a file with an error. One entry relating to it's file listing and mere existence, and then another entry relating to either its hash mismatch or absence from the other directory.  For example, if MyFolderA\FileA.doc in in DirA, whereas in DirB it has a different hash, the user is likely to see:
1. an entry in GridA for for FileA.doc, and 
2. an entry in DridB for FileA.doc, and then 
3. a third entry in GridB relating to the hash mismatch, which does not match what it found for the hash value of FileA.doc in GridA. 
Either way, the user can spot the mis-matched files by sorting the column. This will put the mismatched entries to the top, or the bottom, together. 

The "Compare Directories" tab displayed the filename value in the hash column and the hash value in the filename column! That was fixed. 

DiskModule (for hashing of physical disks) massively improved and based on my sister project YAFFI. Now the interface is much improved and easier to use. Included is the ability to query disk attributes by right clicking and choosing "View Technical Data". 

Uses clause for Disk Module implements a compiler directive to avoid the need to adjust comma positions when compiling on platforms that do not support the disk module, i.e. Linux and Apple macOS.

DiskModule unit updated for use with Freepascal 3.0. Before, any coders wanting to compile QuickHash would have struggled if using FPC 3.0 due to the changes in FwbemObject and specifically the call `while oEnum.Next(1, FWbemObject, nil) = 0 do` which needed to be changed. See comments in source code. 

Program is now set to launch in centre of the "main screen" as defined by windows instead of "desktop centre" as with earlier versions. This means that in the case of multi screen systems, QuickHash will not be split down the middle with half on one screen, half on the other. It will launch in the centre of whichever screen is the main one. 

Changed website URL to the new website of http://quickhash-gui.org 

Moved default copyright and title caption to alongside the website URL. It had been hidden, in error, by a form adjustment in previous versions. 


v2.6.9.2
--------

Minor improvements


v2.6.9.1 - August 2016
----------------------

Fixed a drag n drop error that occurred even when there was no error with dragging and dropping - it was introduced in error with v2.6.9

Converted all file saves in the 'Compare Directories' tab to a streamed creation and save to avoid QuickHash running out of memory during large folder comparisons. Known issue : a strange insertion of data above the top table in HTML mode. 


v2.6.9 - July 2016
------------------

The UNC and long path name fixes still had not entirely worked as hoped when tested on big data sets. Further fixes implemented to ensure the filename and path to an existing file in a very long path is detected, and likewise re-created when copied. 

Improvements made to the way QuickHash reports errors. Errors are generally quite rare except when dealing with very large volumes of network data in a dynamic environment. Prior to v2.6.9, a message window would appear which was not very useful if there were over a few dozen errors because the list was too big for the screen and the automatic saving of that data seemed to go wrong and generate save errors. That was fixed to a simple warning that errors were found and the user is now prompted to save a text file in a place of their choosing.  

If QuickHash fails to initiate a handle to a file at the time of hashing, not only will the user be told that there was an error initiating a handle (as it did before) but it will now tell you which file is causing the problem. 

If the user pastes the path of a mounted drive as a UNC path (e.g. M:\MyServer\MyDataShare\MyFolder) as either source or destination, the user will now be told to fix it to a true UNC path rather than simply crashing out!  

Status bar in the bottom of the Copy tab (the part that shows the user what file is currently being hashed) was being truncated if the path length was particularly long, and was still truncated even if maximised to the full screen size on a 40" monitor! That has been improved. 


v2.6.8 - June 2016
------------------

In the 'Copy' tab, users can now select multiple source folders so that multiple folder content can be hashed, copied to a single destination folder, and then hashed again. Note that an experimental limit exists - if the list of files in memory exceeds 2Gb, QuickHash will likely crash. Please report such instances. If they are too many, I will implement another technique. 

In the copy tab, a bug was fixed for UNC paths when long path names were encountered. Seemingly my earlier efforts to correct this issue had not worked. Now, as of v2.6.8, long paths should not be a problem with UNC mode in the 'Copy' tab for either source or destination locations. 

For Linux users, made the UNC path fields visible, albeit disabled, just to illustrate more clearly to the suer the full path currently selected in the tree view. 

For MD5 and SHA-1 hashes, if the handle to the file fails, a more meaningful error should be displayed rather than a standard error message that didn't tell the user or the developer much as to why the handle failed.  

The 'Stop' button in the 'Copy' tab didn't work at all I noticed! Now it does (it will abort after the file that was being copied at the time of the button press was conducted has been copied, before the next file copy starts). 

The status bar at the bottom of the 'Copy' tab now alerts the user that files are being counted after the user presses 'Go', rather than displaying nothing. 

More of the lists used in memory are Unicode enabled which may reduce crashes. 	


v2.6.7 - Mar 2016
-----------------

The 'Expected Hash' comparison didn't kick in when the user drag and dropped a file into the 'File' tab in that QuickHash wouldn't report to the user whether the computed hash matched what he was expecting though obviously the user could still look by eye at the computed hash but nevertheless, it needed to be fixed. Ticket number 21 refers (https://sourceforge.net/p/quickhash/tickets/21/).

Added a toggle for text line-by-line hashing. Users asked if it would be possible to give them a choice when outputting the results of either including the original source text with the computed hashes or excluding it resulting in a just a list of hashes. So now there is an option that toggles between 'Source text INcluded in output' or 'Source text EXcluded in output'. It, along with the two line-by-line text buttons have been put in their own group box within the 'Text' tab. Non-ASCII\ANSI characters accepted allowing for Western, Eastern and Asian language encoding. Ticket number 22 refers (https://sourceforge.net/p/quickhash/feature-requests/22/)

Some other minor improvements.  


v2.6.6-b - Mar 2016
-------------------

Windows Only: Removed one element from the RAM box because it was reporting incorrect amount of free RAM and it wasn't really that necessary anyway.


v2.6.6 - Jan 2016
-----------------

Added the ability to hash the content of a text file line-by-line (an expansion of the ability to hash pasted text line by line). This means the user can select a file full of a list of names or e-mails addresses or whatever, and each line will be hashed separately. Carriage return line feeds and nulled space should be trimmed from the end of each line. 

Added a RAM status field (Windows only) that updates itself every few seconds with the RAM status of the computer. Useful if particular large data sets are being dealt with. 

Ever since 2011, QuickHash has only been shipped as a 32-bit version for Windows, in the knowledge that all the internal 64-bit requirements are dealt with and the fact that QuickHash doesn't need the extra RAM and so on provided by 64-bit systems. However, a bug was reported (#17 - http://sourceforge.net/p/quickhash/tickets/17/) that highlighted an issue with 32-bit versions of QuickHash running on 64-bit Windows with regard to the content of the Windows\System32 folder. The files in here are presented differently to 32-bit programs than 64-bit ones using the SysWoW64 system. 

"The operating system uses the %SystemRoot%\system32 directory for its 64-bit library and executable files. This is done for backward compatibility reasons, as many legacy applications are hardcoded to use that path. When executing 32-bit applications (like QuickHash, which doesn't need to be 64-bit), WoW64 transparently redirects 32-bit DLLs to %SystemRoot%\SysWoW64, which contains 32-bit libraries and executables. 32-bit applications are generally not aware that they are running on a 64-bit operating system. 32-bit applications can access %SystemRoot%\System32 through the pseudo directory %SystemRoot%\sysnative."
https://en.wikipedia.org/wiki/WoW64 

This means, essentially, that the 32-bit mode of QuickHash, when run on 64-bit systems, is presented with different data to what it is expecting by the filename natively. The users affected by this are minimal (perhaps none except the user who reported it) because it only impacts upon files in that specific folder. Other folders are not affected. Nevertheless, to resolve this, as of v2.6.6, a dedicated 32-bit and 64-bit executable are now provided for Windows. Users are encouraged to use the appropriate executable for their system, but in 99% of cases the 32-bit one should work fine in 32-bit emulated mode, unless the content of C:\Windows\System32 is to be examined.  


v2.6.5 - Dec 2015
-----------------

At user request, the "Text" tab now allows line-by-line hashing of each line. The results are saved to a comma separated text file that can be opened in a text file editor or spreadsheet software.

For example, Google Adwords requires SHA256 lowercase hashes of customer e-mail addresses. So with QuickHash, you can easily paste your list of addresses into the text field, click the "Hash Line-By-Line" button and the output is saved as CSV output for you, ready for use with Google Adwords or any similar product line (https://support.google.com/adwords/answer/6276125?hl=en-GB). Tested with data sets of the low tens of thousands. Would be interested to hear how it copes with larger volumes of data. 

v2.6.4-a Dec 2015 Bug #16 (https://sourceforge.net/p/quickhash/tickets/16/) highlighted an issues with the '"Don't rebuild path' option of the "Copy" tab wherein the copy failed. This was tracked back to v2.6.3 when the new tree view feature was added, replacing the former button path selection functionality. The bug was caused as a result to a path parameter that no longer existed. That was fixed. 


v2.6.4 - Nov 2015
-----------------

QuickHash can now READ and WRITE from and to folders that exceed the MAX_PATH limit of MS Windows, which is 260 characters. A limit of 32K is now adhered to with QuickHash 2.6.4, meaning files may be found on filesystems that were put there by software that is able to bypass the MAX_PATH limit even if regular software like Windows Explorer is unaware of their existence. 

"UNC Mode" added to the "Copy" tab, specifically to enable the user to operate in pure UNC mode but with the new 32K path length limits. Useful for comparing data on multiple network nodes that may not be mapped as a drive letter. Windows only feature (not needed for Linux and Apple Mac). 

The tree view in the copy tab are now sorted alphabetically. 

The "Choose file types" option that has existed in the "Copy" tab for a while has now been added to the "Files" tab by user request. Meaning the user can now recursively hash a folder and sub-folder of files but choose which files to include and which to include. Extension basis only and not file type signature analysis. 

Further GUI anchoring improvements, to make the program display elements better when maximised, with less overlapping hopefully. 

Some historic error messages updated and improved, and made more OS specific.  

User manual updated and revised for v2.6.4

Some other minor improvements


v2.6.3 - Sept 2015
------------------

NEW: Replaced two buttons with two tree view panes in the 'Copy' tab. Left pane is for the user to choose where to copy files FROM. Right pane is for the user to choose where to copy files TO. On appropriate selection, the user needs just press 'Go' and on completion a new form shows the results. 

FIX: In the 'Compare Directories' tab, the save button will now also save the hash comparison result to the log file, i.e. did the comparison match or not? And how many files were counted in grids A and B (feature request #20 http://sourceforge.net/p/quickhash/feature-requests/20/). 

FIX: In the 'Compare Directories' tab, the file counts of the grids and difference counts were overlapping with the labels when high file counts were examined (tens of thousands upwards). Fixed by anchoring the elements. 


v2.6.2.b - August 2015 - Linux only
-----------------------------------

The exclusion of files that were zero bytes (functionality that was introduced in v2.1 back in 2013) meant that block devices in Linux, like /dev/sda or /dev/sda1, were simply ignored if selected by the user and not hashed. A new compiler directive added to ensure that if the file is reported as zero byte that a secondary check is then done to see if its a block device in Linux. If so, it will be hashed providing QuickHash is ran as root or sudo.  


v2.6.2 - August 2015
--------------------

As per feature request #15, and in part request #7, added an 'Expected Hash Value' field to "Text" and "File" tabs to enable the user to paste an already computed hash value (perhaps from another tool, e-mail, webpage) into QuickHash. If the field contains anything other than three dots, once the data hash is generated in QuickHash, it will compare it against the expected hash in this field and report match or mismatch. 

Corrected the fact that that the values for "Total Files in Dir A" and "Dir B" in the comparison of two directories, were the wrong way round. 

Updated copyright date range in the form captions for both the disk hashing module and QuickHash itself

Minor GUI improvements like anchoring.

User manual updated 


v2.6.1 - 31/03/15
-----------------

Added two buttons for copying the grid content of "Compare Directories" to the clipboard, to enable the user to simply paste the results of one or both grids to another tool like Excel, Notepad etc. See ticket ref #9 (https://sourceforge.net/p/quickhash/feature-requests/8/)

Added a "Save to Files" button in the same tab to allow the content of grids A and B to be saved as two separate CSV files (one for each grid) and a single combined HTML file (with the content of table A displayed in one table, the content of table B displayed in the other). 

Throughout all of QuickHash, a line is automatically inserted into both CSV and HTML output stating the name and version of QuickHash used and the date the log file was generated. See ticket ref 7 (https://sourceforge.net/p/quickhash/feature-requests/7/) 

Fixed the truncation of "Total Files in DirA" and "Total Files in DirB" in Compare Directories tab, where counts more than 99 (i.e. 100+) were being truncated. So 150 files was being written as "15". Note this only affected the user display - not the log or display grid. 

Ensured that if the user re-runs a comparison of two directories using the "Compare Directories" tab, any values from the previous comparisons are cleared, such as the values in the display grids, the time ended, the hash match status, etc. Prior to 2.6.1, once a scan had been conducted, the display was not updated until the second scan had finished, as opposed to clearing it at the start of the subsequent scan. 

Added a clickable link to the QuickHash projects homepage at Sourceforge. 


v2.6.0
------

New tab added titled 'Compare Two Files' to allow the user to check if two files in two different places (folders) are identical, or not, without having to hash all the other files in those respective folders. For example, C:\Data\FileA.doc and C:\BackupFiles\FileA.doc

Fixed column misalignment for HTML output of the "FileS" tab; the misalignment was caused by the separation of file name and file path into two different columns in v2.5.2. where the separation in the grid was not carried forward to the HTML output.  

Added the ability to delete duplicate files where found, if the user chooses to detect duplicate files only. 

Further hints corrected in 'Copy' tab. 

Manual updated to incorporate changes brought in versions 2.5.3 and 2.6.0


v2.5.3
------

Further features to try and help users who have a small screen or have set a very low screen resolution. QuickHash will now detect the users screen settings, and, if they are smaller than the default size of QuickHash, QuickHash will be scaled down at the top and the left to that resolution high and wide, less 50 pixels, to be on the safe side. That will, at least, enable such users to get some, if not all, of the functionality from QuickHash and enable them to move it around the screen etc. whereas before, QuickHash would load bigger than the users screen (if they used a small resolution) preventing therm from being able to drag it and resize it. 

Added the ability to move data to very long folders where the total length of the reconstructed folder might exceeded the maximum allowed length of a folder (as dictated by Microsoft Windows, not NTFS) of 260 characters. Not that it only allows the copying of files TO a folder with a length > 260. If the source folder is itself longer than that, the files in those longer folders will not be found yet (will add the ability to do so in later versions). 

Several hints on various buttons and labels corrected to show informative instruction.

The file type mask told users to separate extensions with a space, when no space is needed. In fact, adding a space might case file types not to be found.   

The "Disks" tab was made accessible in the Linux version, but the button disabled and a descriptor to users to just use the "File" tab instead, because users were confused thinking they could use the tab on the Linux platform but they were unsure why it was greyed out. 

When hashing individual files in the "File" tab, if the user single clicked a file, but then clicked 'Cancel', the file was still being passed to the hashing procedures. That was fixed so that if the user cancels, the file is not hashed. 


v2.5.2 - October 2014
---------------------

For the Windows version only: Implemented a scheduler for disk hashing, allowing the user the ability to schedule a start time for their chosen disk. Useful, for example, if a disk is currently being used or examined with an estimated completion time of 2 hours which is after the examining user may have gone home and unable to start the disk hashing process. Now, the user can specify a start date and time that is two or 3 hours after the estimated end time of the other task, and QuickHash will then commence hashing automatically without the need for the user to start it. If no valid start time is entered, the program starts hashing as soon as the chosen disk is double clicked, as normal. 

For all versions: At user request, added an additional column to "FileS" tab to separate the path from the filename. So now the FileName column contains just the filename. And the new 'Path' column contains the files path.  

Added an option in "Copy" tab called "'Don't rebuild path'". If checked, the files in the source directory and all sub-directories will simply be dumped into the root of the destination directory without having the original path rebuilt. Any files with the same name will be appended with 'Filename.ext_DuplicatedFileNameX'. 

Changed progress status labelling to look neater and more compact. 


v2.5.1 - September 2014
-----------------------

The new dynamic text hashing worked fine - new hashes appeared as the user typed, but if the user then chose a different hash algorithm, without changing the text, users felt it would be better for the hash to update dynamically too. So that was applied.

When you clicked in the text area, it was always cleared automatically, for convenience. However, users felt it might be better to only clear the default standing text on entering the text field, rather than always clearing it. So now it only clears it if the default standing text is in the box. After that, it only clears the box if the user consciously clicks the "Clear Text Box" button. This allows the user to add text, then add some more text a few minutes later without losing what they had first.

Drag and drop functionality added for SINGLE FILES in the 'File' tab. So users can now simply drag their file onto QuickHash. Switching the hash algorithm choice in that same tab will dynamically update the hash, as seen with the new text hashing changes reported above. And it will switch the user to that tab, if they do a drag and drop from another tab. Support for folder based drag and drop will not be added. 

Adjusted the 'Started at:' value in 'File' tab from just the time to date and time, to account for large files that may exceed 24 hours to hash. 

All hash value strings assigned as ansistrings. Not strictly necessary as SHA512 as hex is 128 characters, but future algorithms may exceed that. 

Added an advisory to ensure users run QuickHash as administrator for hashing disks and that Windows 8 users might wish to consider other options due to a lack of testing on that rather unpredictable platform. In tests, unexpected read errors were reported on Windows 8. 


v2.5.0 - September 2014
-----------------------

New tab added: 'Hash Compare Directories'. Choose one directory, then choose another directory, and QuickHash will compare one against the other based on the number of files and the hashes of all of those files. If both the file count and all the file hashes match, you can be sure that DirB is an exact copy of the files in DirA. This does not mean that the directory STRUCTURE is exactly the same - only that the files in those directories are the same.

Adjusted text hashing to dynamic output - as you type in the text field, the hash is recomputed. No need to press a button anymore.

The 'Text Hashing' tab, when given lots of data, is now better able to accommodate more data and compute correct hashes without overflow - many Kb is feasible up to a reasonable limit.

Created more meaningful mouse-over hints to each tab, to help users understand what each does. Also renamed them for easier understanding. i.e. 'File' tab, for hashing files. 'FileS' for hashing multiple files. 'Disks' for hashing disks. And so on. 

Assembly coded versions of MD5Transform and SHA1Transform incorporated into source code but NOT the program to allow for more testing. When implemented, tests show that they accelerates QuickHash to one of the fastest (maybe THE fastest?) hashing utility in the world. 1Gb file in 4-6 seconds, hardware permitting. However, portability and CPU architectures need to be better considered before release. 


v2.4.2 - July 2014
------------------

Adjusted interface to make it better on small screens like notebook computers.

Removed a message dialog that appeared when there was an error. Instead, QuickHash will continue when an error is encountered but warn you at the end about the error, instead.  


v2.4.1 - July 2014
------------------

Switched the SHA-1 file hashing functionality to the same transform function as used in the disk hashing module, for speed increases. Meaning QuickHash will compute the hashes of files around 40% faster than in any earlier version.

Customised versions of SHA1 library merged into one unit (called 'sha1customised') that incorporate both the fixes for Unicode file handling and the faster transform routines introduced in the disk hashing module, that are now needed for both disks and files. In v2.4.0, there were two separate customised SHA1 units which made life confusing. 

Entire process repeated for MD5, too. It too has its own customised unit and seems to be around 3 times faster!! 

Start Times and End Times provided as a pair, making them more useful and where possible computing the time actually taken to do the task. 

Fixed status bar - the status bar in 'File Hashing' was being populated by 'Hash, Copy, Hash' processes instead of just the 'File Hashing' progress tab. The status bar in 'Hash, Copy, Hash' was not being populated. That was fixed. 

Redundant Unit1 code (applied to versions prior to v2.0) removed. 


v2.4.0 - July 2014
------------------

After several years of trying, the functionality to hash physical disks in Windows is now part of QuickHash. It has been implemented by means of a separate self-contained module that is launched on press of a button in the fourth tabsheet titled "Disk Hashing (for Windows)". The Linux version does not need this tab or this module so neither are available to Linux users. Linux users have always had the option of hashing disks with QuickHash by running it as root or sudo and using the "Hash File" tabsheet and navigating to /dev/hdX or /dev/sdaX or whatever. Note SHA1 only, for now. Others will follow in X.X.X sub releases, e.g. 2.4.1. Speeds are fast - approx 3.5Gb per minute via Firewire800 and up to 8Gb per minute with direct SATA connection. 

Some redundant unused variables removed to optimise memory usage.

Some minor improvements to the interface - a few buttons moved around, extra hints added etc.


v2.3 - June 2014
----------------

Complete support for Unicode on Windows, ensuring filenames or directories containing Chinese or Arabic or Hebrew (etc) characters can now be processed using QuickHash without the user having to change their language and region settings. Prior to this, QuickHash was generating the default initialisation hashes for such files but not actually hashing them. All Windows users are encouraged to discard any version prior to v2.2 and adopt v2.3. 


v2.2 - Nov 2013
---------------

It was reported that large files failed to hash properly with SHA256 or SHA512 implementation. It turned out this was due to a 32-bit integer declaration in the DCPCrypt library that is used by QuickHash for those two algorithms. Updated by using QWord instead Longword variables. Output checked against SHA256SUM and SHA512SUM and found to be OK now. 

Linux version brought to same level as Windows version. Interface improved to better display values.  


v2.1 - June 2013
----------------

All versions prior to 2.1 suffered a 32-bit 4Gb limitation when copying (as part of the 'Hash, Copy, Hash' routine) a single file larger than 4Gb. That was fixed by casting the "filesize" variable to Int64 instead of Int32 meaning the size limitation is now set by your filesystem only (16 Exabytes for NTFS). 

International language support added for filenames and directories that contain or might be created of a non-English nature by use of UTF8 casting. For example, the destination directory for "Hash, Copy, Hash" can now contain non-English characters. 

All hashing in Quick Hash utilises Merkle-Damgård constructions (http://en.wikipedia.org/wiki/Merkle%E2%80%93Damg%C3%A5rd_construction). As such, zero byte files will always generate a predetermined hash, depending on the algorithm; SHA-1, for example, is always da39a3ee5e6b4b0d3255bfef95601890afd80709. To avoid confusion, if a file is zero bytes, it is not hashed at all and the entry 'Not computed, zero byte file' is entered into the results. Though I acknowledge some users may feel it is necessary to hash zero byte files for security reasons, on the whole, I don't think it is for 99% of users. 

Files of zero bytes are now copied as part of the "Hash, Copy, Hash" routine to facilitate those who wish to use QuickHash as a backup system where, on occasion, zero byte files are created by software and are required in order to function properly. 

Date format of output directory changed again to 'yy-mm-dd_hhmmss' (e.g. QuickHash_13-12-25_221530) due to the now widespread use of QuickHash internationally. The previous format of ddmmyy worked OK for UK users, but there is some merit in the year, month, day format, especially for multiple output dirs. 


v2.0 - Feb 2013
---------------

Interface entirely re-written to use tabbed design with each hashing feature having its own parent tab. Allowing the util to be used on low resolution screens. Default size is now ~900 x ~1000 pixels meaning it should be visible on every screen but the smallest of resolutions. This work has made the exe leaner with less decision loops and less code.  

Status fields that record % progress, Mb copied etc are cleared after an earlier run

Simple text hashing now has a much larger area for larger text segments and the hash value field is larger allowing SHA256 and SHA512 to be seen in full.

Status bars more neatly attributed to each individual process to ensure they are kept in place during resizing. 

All necessary fields (source directory path fields, grid displays, text areas etc) that a user may want to make wider when the GUI is maximised are now all right aligned meaning they'll grow when the GUI is maximised. Note, though, that the v2 interface is designed to be now 850 pixels wide.

Date format displayed as dd/mm/yy hh/mm/ss instead of dd/m/yy hh/mm/ss for ease of reading the systems date and time settings (that are reported to the user for some functions) that QuickHash is running on and to ensure the output directory is easier to read. The destination dir for copy and hash processes now read "QuickHash_ddmmyy-hhmmss". 

Moved some of the tick boxes into a panel group to help with resizing and moved the status bars of recursive directory hashing further in to the left.


v1.5.6 - Jan 2013
-----------------

The display grids for displaying hashes of multiple files in a directory and for "copy and paste" hashing now have the number of rows pre-computed based on the number of files found prior to hashing. This saves a considerable amount of time with large data sets. 

Combined with the step above, a gigantic speed improvement caused by also disabling the dynamic bottom pane until after all files are hashed. Having it refresh for every file was not really necessary anyway, given that the status bar reports the file being hashed and the progress stats show files %, data volume etc. benchmarks show 3K files took 2 minutes with version < v1.5.6; With v1.5.6, the same 3K files take 12 seconds! 

The same visibility change applied to recursive copy and hash, though, in tests, the process of copying the files was slower than the grid display but with lots of small files, this is likely ot have made an improvement. 

With regard to recursive directory hashing and recursive copy and hashing; the user can now decide to override the default behaviour of hashing all files in all sub-directories of that chosen directory, meaning that just the files in the root of that chosen directory can be hashed (and copied if appropriate) and no others in other sub directories, if required. 

The user can now decide whether to flag any duplicate files found, or not (only for standard directory hashing - not for copy and hash, yet).

The left to right scroll bar of the bottom pane was partly obscured by the status bar. That was corrected. 


v1.5.5 - Nov 2012
-----------------

Added file mask capability to allow selective searching for one or more mixed file types, e.g. *.doc; *.xls etc. New masks can be added at will. 

Added progress indicators to recursive copy and hash, to match the standard recursive hash without copy. 

A new intermediary output directory, named after the date and time of execution, is now added beneath the output directory with the output then put beneath that ensuring that if multiple outputs are sent to the same directory at different times, each output can easily be identified. 

A log of file of files that failed to copy or those for whom the hashes didn't match are now recorded in the chosen output directory.

Adjusted phrasing of Clipboard button to "Clipboard Results", to mean "Copy the results to RAM clipboard" because the previous phrasing of "Copy to RAM" was misleading, suggesting the files would be copied to RAM, which was not true.

Improved layout slightly by replacing some labels with edit fields. 

Improved the 'Hash mismatch' error to make it easier to read and including the name of the actual file that has failed, as well as just the hash value. 

Added a warning to recursive copy and hash feature that OS protected files or files in use will not copy properly, to make the user choose more wisely


v1.5.4.1 - Nov 2012
-------------------

All functionality added since 1.5.2.2 added for the Linux version, too, matching it to the 1.5.4 Windows release
* Note date and time attributes of recursive directory copy and paste adjusted as only

Last Modified dates are available in Linux

Added Stop button to recursive directory copy and paste traversal (top right pane), to match the stop features of the simpler recursive directory traversal functionality (bottom pane)


v1.5.4 - May 2012
-----------------

As announced in v 1.5.3, improved the "Copy and Hash Files" display area as follows:
* The display area is now a numerical grid with sortable columns instead of a text field.
* Faster and more feature rich options and responsiveness
* For Windows only instances of QuickHash, the source files' created, last modified and last accessed dates are looked up, displayed and logged to account for NTFS\FAT32 issues with date attribute retention
* Added the ability to export results to HTML file, including column headings
* Added the ability to copy the grid content to clipboard for easy pasting into spreadsheets etc

Some minor code improvements and interface labelling all round 


v1.5.3 - May 2012
-----------------

Improved the 'Recursive Directory Hashing' display grid as follows:
* Added ability to sort by file name, hash value or file size
* Added ability to drag columns from left to right
* Added ability to auto-expand column width to max content of largest cell by double click the column dividers at completion
* Added a 'Copy to Clipboard' button (it is still possible to to copy a cell or range of cells by selecting and Ctrl+C them). 
* Improved the labelling and layout to make it more consistant with the font of the rest of the application

v1.5.2.2 - April 2012
---------------------

Fixed incorrect formatting of reported date and time settings to now accurately show DD/MM/YY HH:MM:SS

Converted display area of "Copy & Hash Files" to a listbox, rather than a memo field to increase speed

Adjusted "Copy & Hash Files" delimiter to a tab (#9) instead of nothing to allow easier importing into spreadsheets

Coming Soon: v 1.5.3 will use a grid system for the "Copy & Hash Files" display instead of either a memo field or a listbox


v1.5.2.1 - March 2012
---------------------

Minor improvement


v1.5.2 - March 2012
-------------------

System Error codes returned with any last error to enable better dev support to users GUI set to increase proportionally as the interface is maximised to the max screen size to allow more data to fit in the meo fields when run on larger screens.

The 1.5.0 feature of copying source files to destination directories further corrected and improved as follows:
* Radio box added to choose whether to list JUST directories or whether to list JUST directories AND files, neither of which will be hashed or copied. Useful for occasions when the user might want to generate a list of subdirectories only, that might contain forensic images for example, that they wish to paste into the case properties of forensic software   like X-Ways Forensics or FTK or into a report.
* Interface refresh following copy errors or hash mismatch errors to avoid the error message hanging about after clicking OK. 

KNOWN ISSUES: 
* Some Chinese Unicode characters cause the copy to fail. Need to implement special Unicode vars for that type of code.
* Illegal file names containing special chars or whose name exceeds the maximum windows length can cause the copy to fail


v1.5.1 - March 2012
-------------------

Main Menu added - About page, Credits page and a "File --> Exit" to free space on the form by allowing the removal of the 'Exit' button

Italian version - credit to Sandro of the DEFT Live CD project for translating the English to Italian - www.deftlinux.net/

Corrected keyboard shortcut keys as some shortcuts were applied twice to different buttons. 

Minor re-alignment of GUI panes

The 1.5.0 feature of copying source files to destination directories corrected and improved as follows:
* The "Go!" button is disabled if either the source and destination directories are not chosen or if they are invalid or, in the case of the "Just generate recursive list of dirs and files" being ticked, the Source destination has to be valid at least. If not, the button stays greyed out. 
* The "X number of files found. Proceed?" message dialog continued even if the user selected 'No'. That was fixed. 
* The "X number of files found. Proceed?" dialog now shows the host system date and time, too. 
* The summary information that states how many files were copied, the number of errors (if any) and the number of hash mismatches (if any) is now inserted at the top of the log file, if created. 
* Date and time of the host system is determined and logged at the time the copying process is started.


v1.5.0 - March 2012
-------------------

Recursive directory copying and hashing from source directory to destination directory added.

Some minor GUI re-arrangement and improvement for readability. 

Known Issues: Some unicode filenames cause an error, but not all. Also, illegal Windows characters in the filename may cause an error.   


v1.4.1 - December 2011
----------------------

Took out the autosize attribute for the grid display of recursive directory file hashing. Refreshing that grid with tens of thousands of files slowed down the program considerably - sometimes up to a third! 

Added a 'Counting files...' entry in the progress bar at the bottom of the grid display so that when a directory is first selected, the user now knows the program is working while it calculates how many files there are to hash in total, as opposed to appearing to be doing nothing. 


v1.4.0 - November 2011
----------------------

Added MD5, SHA256 and SHA512 hashing algorithms in the form of a radio button selection by using the DCPCrypt library

Added a status bar to make it more obvious which file is currently being hashed

Refined the labels of individual file hashing so that they are cleared and refreshed if a subsequent file is hashed without restarting the program

Some minor improvements to source code readability and layout. 


v1.3.2 - Sept 2011
------------------

Ability to export results to HTML web file added. The user can now export the results to just HTML format, or just to CSV format, or both. 

Minor improvement to the prompt for log file credentials to ensure that if the user cancels that decision, the program does not crash and instead gracefully returns to the grid display. 


v1.3.1 - Sept 2011
------------------

Text field is now cleared as soon as clicked for text input, avoiding the need to manually delete the "Type text here..." message, thus reducing risk or cross contamination

Any accidentally left white space to the right or left of the first or last character of the string is stripped before hashed. Spaces that form part of the string are not removed. 


v1.3.0 - Sept 2011
------------------

During recursive directory hashing, the display grid now keeps up with the files as they are been hashed so the user can see what file is currently being analysed

Option of saving the content of the display grid as a CSV text file


v1.2.1 - July 2011
------------------

The data figure next to total files examined looped back round to zero with unusually large files. This was fixed by using a QWord integer and QuickHash can now recursively SHA1 hash directories containing 18 ExaBytes (250 thousand 4 TeraByte harddisks full of data). 


v1.2 - June 2011
----------------

String hash box enlarged to allow paragraphs or long sentences to be hashed, instead of just a few words. 

File hashing now has a start and end time counter, to determine how long the hashing process took.

Recursive directory and file hashing now has a start and end time counter, to determine how long the hashing process took for entire directory and its children.

Recursive directory and file hashing now has a field to show the total amount of data examined (bytes, Kb, Mb, Gb or Tb).

Linux version optimised for Linux usage

Windows version optimised for Windows usage

Minor improvements relating to layout and code optimisation.


v1.1.1 - June 2011
------------------

Improvements to the layout of the interface, some grammatical corrections and refinement of column labelling etc. 


v1.1 - June 2011
----------------

Larger buffers allow faster hashing of files over 1Mb. 

Files without an extension are now detected. 


v1.0 - May 2011
---------------

Hashing of a string

Hashing of a single file (or disk if ran in Linux using sudo or root permissions)

Hashing of an entire directory - its children and al sub-directories, including a percentage progress indicator. 

Copy and Paste to Clipboard

