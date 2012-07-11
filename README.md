Code-Samples
============

My code samples

<html> 

<head> 

<title>Web Search</title> 

<meta http-equiv="Content-Type" content="text/html; charset=gb2312"> 

</head> 

<body bgcolor="#FFFFFF" text="#000000"> 

<form name="form1" method="post" action="search.php"> 

<table width="100%" cellspacing="0" cellpadding="0"> 

<tr> 

<td width="36%"> 

<div align="center"> 

<input type="text" name="keyword"> 

</div> 

</td> 

<td width="64%"> 

<input type="submit" name="Submit" value="Search"> 

</td> 

</tr> 

</table> 

</form> 

</body> 

</html> 

Step 2: Write search.php file. It is the core of your website search engine. 

Under your website root, create a file called search.php or anything you like. <?php 

//get keywords 

$keyword=trim($_POST["keyword"]); 

//check if the keyword is empty 

if($keyword==""){ 

echo"no keywords"; 

exit; 

} 

?> 

With above, you can give hints to your users when they forget to enter a keyword. Now let's go through all the files or articles in your website. 

<?php 

function listFiles($dir){ 

$handle=opendir($dir); 

while(false!==($file=readdir($handle))){ 

if($file!="."&&$file!=".."){ 

//if it is a directory, then continue 

if(is_dir("$dir/$file")){ 

listFiles("$dir/$file"); 

} 

else{ 

//process the searching here with the following PHP script 

} 

} 

} 

} 

?> 

The following scripts read, process files and check whether the files contain $keyword. If $keyword is found in the file, the file address will be saved in an array-type variable. 

function listFiles($dir,$keyword,&$array){ 

$handle=opendir($dir); 

while(false!==($file=readdir($handle))){ 

if($file!="."&&$file!=".."){ 

if(is_dir("$dir/$file")){ 

listFiles("$dir/$file",$keyword,$array); 

} 

else{ 

//read file 

$data=fread(fopen("$dir/$file","r"),filesize("$dir/$file")); 

//avoid search search.php itself 

if($file!="search.php"){ 

//contain keyword? 

if(eregi("$keyword",$data)){ 

$array[]="$dir/$file"; 

} 

} 

} 

} 

} 

} 

//define array $array 

$array=array(); 

//execute function 

listFiles(".","php",$array); 

//echo/print search results 

foreach($array as $value){ 

echo "$value"." 
\n"; 

} ?> 

Now, combine the programs listed above, you will find all the related results in your websites will be found and listed. A further optimization of the search engine can be taken by adding the following, 

1,list the title of all searching results 

REPLACE THE FOLLOWING 

if(eregi("$keyword",$data)){ 

$array[]="$dir/$file"; 

} 

WITH 

if(eregi("$keyword",$data)){ 

if(eregi("<title>(.+) 

</title>",$data,$m)){ 

$title=$m["1"]; 

} 

else{ 

$title="no title"; 

} 

$array[]="$dir/$file $title"; 

} 

2,Add links to searching results 

CHANGE THE FOLLOWING 

foreach($array as $value){ 

echo "$value"." 
\n"; 

} 

TO 

foreach($array as $value){ 

list($filedir,$title)=split("[ ]",$value,"2"); 

echo "$value"." 
\n"; 

} 

3 Set time limit for PHP execution 

ADD THE FOLLOWING AT THE BEGINNING OF PHP FILES 

set_time_limit("600"); 

The above unit is second，so ten minutes is the litmit. 

Now, combine all the above programs and get the complete search.php file as following, 

set_time_limit("600"); 

$keyword=trim($_POST["keyword"]); 

if($keyword==""){ 

echo"Please enter your keyword"; 

exit; } 

function listFiles($dir,$keyword,&$array){ 

$handle=opendir($dir); 

while(false!==($file=readdir($handle))){ 

if($file!="."&&$file!=".."){ 

if(is_dir("$dir/$file")){ 

listFiles("$dir/$file",$keyword,$array); 

} 

else{ 

$data=fread(fopen("$dir/$file","r"),filesize("$dir/$file")); 

if(eregi("<body([^>]+)>(.+)</body>",$data,$b)){ 

$body=strip_tags($b["2"]); 

} 

else{ 

$body=strip_tags($data); 

} if($file!="search.php"){ 

if(eregi("$keyword",$body)){ 

if(eregi("<title>(.+)</title>",$data,$m)){ 

$title=$m["1"]; 

} 

else{ 

$title="no title"; 

} 

$array[]="$dir/$file $title"; 

} 

} 

} 

} 

} 

} 

$array=array(); 

listFiles(".","$keyword",$array); 

foreach($array as $value){ 

list($filedir,$title)=split("[ ]",$value,"2"); 

echo "$title "." 
\n"; 

} ?> 
